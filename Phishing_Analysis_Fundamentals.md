# Phishing Analysis Fundamentals

## The Email Address

1. User Mailbox (or Username)
2. @
3. Domain  
`billy@johndoe.com`  
1. The user mailbox is billy
2. @ (thanks Ray)
3. The domain is johndoe.com

## Email Delivery
- SMTP (Simple Mail Transfer Protocol) - It is utilized to handle the sending of emails. 
- POP3 (Post Office Protocol) - Is responsible transferring email between a client and a mail server. 
- IMAP (Internet Message Access Protocol) - Is responsible transferring email between a client and a mail server.   

POP3 vs IMAP  
- POP3
    - Emails are downloaded and stored on a single device.
    - Sent messages are stored on the single device from which the email was sent.
    - Emails can only be accessed from the single device the emails were downloaded to.
    - If you want to keep messages on the server, make sure the setting "Keep email on server" is enabled, or all messages are deleted from the server once downloaded to the single device's app or software.  
- IMAP
    - Emails are stored on the server and can be downloaded to multiple devices.
    - Sent messages are stored on the server.
    - Messages can be synced and accessed across multiple devices.  

    <img src="https://github.com/nkn-ctrl/pushtest/blob/main/email-network-flow-4.png" width="700">  
<br>

[E-mail PORT](https://help.dreamhost.com/hc/en-us/articles/215612887-Email-client-protocols-and-port-numbers)


## Email Headers

Email header fields:  
1. From - the sender's email address
2. Subject - the email's subject line
3. Date - the date when the email was sent
4. To - the recipient's email address  
    <img src="https://github.com/nkn-ctrl/pushtest/blob/main/email-raw-headers-2b.png" width="800">  

1. X-Originating-IP - The IP address of the email was sent from (this is known as an X-header)
2. Smtp.mailfrom/header.from - The domain the email was sent from (these headers are within Authentication-Results)
3. Reply-To - This is the email address a reply email will be sent to instead of the From email address
<br>

[Media Temple on how to analyze email headers](https://mediatemple.net/community/products/all/204643950/understanding-an-email-header)

## Email Body
<img src="https://github.com/nkn-ctrl/pushtest/blob/main/email-with-attachment-2.png" width="600">  

From the above example, we can see the headers associated with this attachment:
- Content-Type is application/pdf. 
- Content-Disposition specifies it's an attachment. 
- Content-Transfer-Encoding tells us it's base64 encoded. 

Reconstruct the PDF using the base64 data.  
    Use Cyberchef from Base64 and save the output as a pdf file. Open the pdf.  
<img src="https://miro.medium.com/max/720/1*6kW7iS8roG616-1hU5Ly5g.webp" width="650">

