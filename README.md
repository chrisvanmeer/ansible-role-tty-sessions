# TTY Sessions

An Ansible role to monitor open TTY (physical console) sessions on Linux hosts
and optionally email a report. Sessions are collected per host with `who` and
combined into a single report on the Ansible control node.

## Requirements

- Ansible core 2.15 or newer on the control node.
- The `community.general` collection on the control node, used to send mail.
- When email reporting is enabled, the SMTP relay only needs to be reachable
  from the Ansible control node, not from the managed hosts.

## Role Variables

### Email report

| Variable | Default | Description |
|---|---|---|
| `tty_session_email_enabled` | `false` | Set to `true` to send an email report when TTY sessions are detected. |
| `tty_session_email_subject` | `Open TTY sessions` | Subject of the report. |
| `tty_session_email_subtype` | `html` | MIME type of the report, `html` or `plain`. |
| `tty_session_email_sender` | (unset) | From address. Required when email reporting is enabled. |
| `tty_session_email_recipient` | (unset) | To addresses, single address or comma separated list. Required when email reporting is enabled. |
| `tty_session_email_recipient_cc` | (unset) | Optional carbon copy recipients. |
| `tty_session_email_recipient_bcc` | (unset) | Optional blind carbon copy recipients. |
| `tty_session_email_headers` | (unset) | Optional extra email headers as a dictionary. |
| `tty_session_smtp_server` | (unset) | SMTP relay address. Required when email reporting is enabled. |
| `tty_session_smtp_port` | (unset) | SMTP relay port, defaults to 25. |
| `tty_session_smtp_username` | (unset) | Optional username for authenticated SMTP. |
| `tty_session_smtp_password` | (unset) | Optional password for authenticated SMTP. |
| `tty_session_smtp_secure` | (unset) | SMTP encryption, one of `auto`, `starttls`, `starttls/assume` or `no`. |

Variables left unset are omitted when sending the mail, so the module defaults
apply. The optional variables are documented as `null` in `defaults/main.yml`.

## Email is sent from the control node

The report is always sent from the Ansible control node (`delegate_to:
localhost`), once for all hosts combined, and never with `become` privileges.
Managed hosts often cannot reach the SMTP relay while the control node can, and
sending mail does not require elevated privileges. Only SMTP settings that are
reachable from the control node are used.

## Dependencies

- `community.general` on the control node, for the `community.general.mail`
  module used to send the report.
- For testing with Molecule: `community.docker` and `ansible.posix`.

## Example Playbook

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

## Testing

The repository ships a Molecule scenario that runs the role against a Debian
container and, with a Mailpit sidecar bound to the control node loopback,
proves the email report is sent from the control node:

```bash
pip install molecule "molecule-plugins[docker]" docker ansible-core
ansible-galaxy collection install -r requirements.yml
molecule test
```

## License

BSD

## Author Information

- Chris van Meer <c.v.meer@atcomputing.nl>