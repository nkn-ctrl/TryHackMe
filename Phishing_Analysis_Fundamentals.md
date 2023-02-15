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

