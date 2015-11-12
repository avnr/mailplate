Send Muli-Language Multi-Transport Template-Driven Email - mailplate
===

With `mailplate` you can send an email from a template stored in a gettext `.mo` file, over any
supported transport. Current transports include SMTP, SMTPS, Amazon SES and Mailgun. It is very easy to add
a "driver" for additional transports.

Installation
---

Simplest installation is with `pip`:

    pip install mailplate

Then in your code import the Mailplate class:

    from mailplate import Mailplate

Requires Python3.

Using `mailplate`
---

**STEP 1 - define a mailer:**

    mailplate = Mailplate(
        'smtps://smtp.gmail.com:587/',       # The transport; can be also 'ses://', 'mailgun://',...
        ( 'myemail@gmail.com', 'secret123' ) # Username and password
        'myemail@gmail.com',                 # The "From" address
        'locale',                            # gettext locale directory
        'myapp',                             # gettext domain
        'en_US',                             # Default language
        [ 'msg1', 'msg2', 'msg3' ])          # List of app's email messages

The email templates in the above example are stored under the directory structure
`locale/-lang-/LC_MESSAGES/myapp.mo` where `-lang-` is each one of the application's supported
locales. For each email message the `.mo` files should include the following msgid-s:

- The email subject, whose id is the message name appended with `':subject'`. In the above example
the msgid of the messages are `msg1:subject`, `msg2:subject`, and `msg3:subject`.

- The email body, whose id is the message name appended with `':text'` for the text version of the
message body and `':html'` for the html version of the message body. At least one version is
required, it is recommended to provide both. In the above example the msgid of the messages are
`msg1:text`, `msg2:text`, and `msg3:text` for the text version and `msg1:html`, `msg2:html`, and
`msg3:html` for the html version. The `mailplate` package contains an `example` directory with a
small sample of language files.

**STEP 2 - send the message:**

    mailplate. send(
        'someone@example.com', # Recepient address
        'msg1',                # The message name
        'fr_CA',               # The language
        { 'someval': 123 })    # Optional arguments

The `send` method will send the requested message in its requested-language version. If there is no
version of the message in the requested language and the requested language is country-specific, then
`mailplate` will attempt to find the language under the country-neutral version - in the above
example under the language `'fr'`. If the message is not found in the requested language then
`mailplate` will use the version in the default language which was set in the class initializer.

The `send` method can take an optional arguments list which consists of parameter names and their
values. If the message contains a reference to the parameter in the form `...{param}...` then the
reference will be replaced by the parameter's value in the arguments list. In the above example, each
reference `{someval}` will be replaced by the `123` string. `mailplate` automatically generates an
argument for the parameter `to` which evaluates to the recipient's email address.

Transport-Specific Stuff
---

The current `mailplate` version supports the trasports: SMTP, SMTPS, Amazon SES and Mailgun. The
transport is set by the first two arguments of the Mailplate class initializer. The first argument is
a URL-style mailer address and the second argument is transport-sepecific credentials, as follows:

- SMTP: The mailer URL is of the form `smtp://host:port` where port can be omitted and defaults to 25.
The credentials are ignored. This transport is useful primarilly for testing purposes.

- SMTPS: The mailer URL is of the form `smtps://host:port` where port can be omitted and defaults to 465.
The credentials consists of two items - the username and the password.

- Amazon SES: The mailer URL is `ses://`. The credentials consist of three items: The Access Key
ID, the Secret Access Key, and the AWS region. Using SES requires the `boto` package to be installed.

- Mailgun: The mailer URL is `mailgun://`. The credentials consist of the region and the API key.
Using Mailgun requires the `requests` package to be installed.

The `mailplate.py` file has at its bottom a few commented sample calls which can be edited for a
quick hands-on. They include sample definitions for each of the above transports.

Adding More Transports
---

Every transport has a "driver" which is a file in the `driver` directory. Check out the provided
drivers to write your own, they are ridiculously simple.

Help and Reporting Issues
---

Please use <https://github.com/avnr/mailplate/issues>

License
---

MIT License

Release Notes
---

Release 0.6, 2015-11-11

- Added the SMTP transport

Release 0.601, 2015-11-12

- Fixed setup to include the drivers
