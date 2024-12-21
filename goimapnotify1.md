# NAME

Go IMAP notify - Wait for IMAP mailbox changes (new/deleted/updated messages)
using IDLE, and execute scripts.

# SYNOPSIS

  goimapnotify -conf [string]  
  goimapnotify -debug  
  goimapnotify -list  
  goimapnotify -wait [int]  

# DESCRIPTION

This application is mostly compatible with the configuration of imapnotify made
with Python (be sure to change password_eval to passwordCMD, see issue #3), the
following are all options available for the configuration:


# USAGE

  -conf string  
        Configuration file (default "${HOME}/.config/goimapnotify/goimapnotify.yaml")  

  -debug  
        Output all network activity to the terminal  

  -list  
        List all mailboxes and exit  

  -wait int  
        Period in seconds between IDLE event and execution of scripts (default 1)  

# CONFIGURATION

On first start, the application will run onNewMail and onNewMailPost and then wait for events from your IMAP server.

    onNewMail: is an executable or script to run when new mail has arrived.
    onNewMailPost: is an executable or script to run after onNewMail has ran.
    onDeletedMail: is an executable or script to run when mail has been delete.
    onDeletedMailPost: is an executable or script to run after onDeletedMail has ran.
    hostCMD: is an executable or script that retrieves your host from somewhere, we cannot pass arguments to this command from Stdin.
    usernameCMD: is an executable or script that retrieves your username from somewhere, we cannot pass arguments to this command from Stdin.
    passwordCMD: is an executable or script that retrieves your password from somewhere, we cannot pass arguments to this command from Stdin.
    xoAuth2: is an option that allow us to login on your IMAP using OAuth2, be aware: the token is retrieve from passwordCMD (see #9).
    wait: is the delay in seconds before the mail syncing is trigger (see #10).
    boxes: List of mailboxes. If none is defined, all will be monitored.

The application will use TLS as long as the IMAP server advertises this capability. If you use self-signed certificates or something, be sure to set rejectUnauthorized as false. To enable TLS connection, set tls as true and starttls as false

If your host do not offer IDLE, a sane default of checking every 15 minutes will take place instead.

You can also use xoAuth2 instead of password based authentication by setting the xoAuth2 option to true and the output of a tool which can provide xoAuth2 encoded tokens in passwordCMD. Examples: Google oauth2l or xoauth2 fetcher for O365.

# EXAMPLE

    configurations:
        -
            host: example.com
            port: 143
            tls: true
            tlsOptions:
                rejectUnauthorized: false
                starttls: true
            username: USERNAME
            alias: ExampleCOM
            password: PASSWORD
            xoAuth2: false
            boxes:
                -
                    mailbox: INBOX
                    onNewMail: 'mbsync examplecom:INBOX'
                    onNewMailPost: SKIP
        -
            hostCMD: COMMAND_TO_RETRIEVE_HOST
            port: 993
            tls: true
            tlsOptions:
                rejectUnauthorized: true
                starttls: true
            username: ''
            usernameCMD: ''
            password: ''
            passwordCMD: ''
            xoAuth2: false
            onNewMail: ''
            onNewMailPost: ''
            onDeletedMail: ''
            onDeletedMailPost: ''
            boxes:
                -
                    mailbox: INBOX
                    onNewMail: 'mbsync examplenet:INBOX'
                    onNewMailPost: SKIP
                -
                    mailbox: Junk
                    onNewMail: 'mbsync examplenet:Junk'
                    onNewMailPost: SKIP

  [1]: https://gitlab.com/shackra/goimapnotify
