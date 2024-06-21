ansible-role-tty-sessions
========================

A role to monitor open TTY sessions in Linux hosts.

Requirements
------------

`jmespath`

Role Variables
--------------

```yml
tty_session_email_enabled: false
```

Default email reporting is disabled, set this to `true` to enable.

```yml
tty_session_email_subject: "Open TTY sessions"
```

Email subject when sending email reports.

```yml
tty_session_email_subtype: "html"
```

Setting the email mime type to `html`. Can also be set to `plain`. One can modify the template to their choosing to match this.

There are more variables available for the email section. Refer to the last task in the playbook for this. If not existent, these will be omitted, but this gives you the option to include these values in variables, rather than having to edit the playbook.


Dependencies
------------

For email, this role depends on the `community.general.mail` module.

Example Playbook
----------------

Including an example of how to use your role (for instance, with variables passed in as parameters) is always nice for users too:

```yml
- name: TTY session monitoring
  hosts: all
  become: true

  vars:
    tty_session_email_enabled: true
    tty_session_email_subject: "Open TTY sessions"
    tty_session_email_sender: "tty.sessions@yourdomain.com"
    tty_session_email_recipient: "soc@yourdomain.com"
    tty_session_smtp_server: "smtp.yourdomain.com"
    tty_session_smtp_port: 25

  roles:
     - role: chrisvanmeer.tty_sessions
```

License
-------

BSD

Author Information
------------------

- Chris van Meer <c.v.meer@atcomputing.nl>
