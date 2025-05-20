
DISCLAIMER - ABANDONED/UNMAINTAINED CODE / DO NOT USE
=======================================================
While this repository has been inactive for some time, this formal notice, issued on **December 10, 2024**, serves as the official declaration to clarify the situation. Consequently, this repository and all associated resources (including related projects, code, documentation, and distributed packages such as Docker images, PyPI packages, etc.) are now explicitly declared **unmaintained** and **abandoned**.

I would like to remind everyone that this project’s free license has always been based on the principle that the software is provided "AS-IS", without any warranty or expectation of liability or maintenance from the maintainer.
As such, it is used solely at the user's own risk, with no warranty or liability from the maintainer, including but not limited to any damages arising from its use.

Due to the enactment of the Cyber Resilience Act (EU Regulation 2024/2847), which significantly alters the regulatory framework, including penalties of up to €15M, combined with its demands for **unpaid** and **indefinite** liability, it has become untenable for me to continue maintaining all my Open Source Projects as a natural person.
The new regulations impose personal liability risks and create an unacceptable burden, regardless of my personal situation now or in the future, particularly when the work is done voluntarily and without compensation.

**No further technical support, updates (including security patches), or maintenance, of any kind, will be provided.**

These resources may remain online, but solely for public archiving, documentation, and educational purposes.

Users are strongly advised not to use these resources in any active or production-related projects, and to seek alternative solutions that comply with the new legal requirements (EU CRA).

**Using these resources outside of these contexts is strictly prohibited and is done at your own risk.**

This project has been transfered to Makina Corpus <freesoftware-corpus.com> ( https://makina-corpus.com ). This project and its associated resources, including published resources related to this project (e.g., from PyPI, Docker Hub, GitHub, etc.), may be removed starting **March 15, 2025**, especially if the CRA’s risks remain disproportionate.

Nslcd
=====

This role installs and configures [nslcd](https://arthurdejong.org/nss-pam-ldapd/) and PAM to enable user authentication against an LDAP directory on the target system.

Requirements
------------

This role requires Ansible 2.8 but has no further requirements on the controller.

Role Variables
--------------

* `nslcd_uri`  
  A list of LDAP server URIs to connect to.
  Normally, only the first server will be used with the following servers as fall-back.
  Mandatory.
* `nslcd_starttls`  
  Boolean.
  Whether to use STARTTLS to connect to the LDAP server.
  Defaults to `false`.
* `nslcd_tls_ca_path`  
  Path to a directory containing trusted CA certificates.
  Default value depends on the target system's operating system, but generally points to the system certificate store.
* `nslcd_tls_cert`  
  Path to the client certificate to use to connect to the LDAP server.
  Optional.
* `nslcd_tls_cert_key`  
  Path to the private key for `nslcd_tls_cert`.
  Mandatory if `nslcd_tls_cert` is set.
* `nslcd_binddn`, `nslcd_bindpw`  
  The DN and password with which to bind to the LDAP server for lookups.
  If not set, nslcd will bind anonymously.
  Optional.
* `nslcd_rootdn`, `nslcd_rootpw`  
  The DN and password with which to bind to the LDAP server when to `root` user changes a user's password.
  If the password is not given, the PAM module prompts the user for it.
  Optional.
* `nslcd_base`  
  A list of base DNs to use as the search base for LDAP lookups.
  Mandatory.
* `nslcd_user_base`, `nslcd_group_base`  
  A list of search bases specific to user or group lookups, respectively.
  Optional.
* `nslcd_user_scope`, `nslcd_group_scope`  
  The search scope for users or groups, respectively.
  Valid values are `subtree`, `onelevel`, `base` and `children`.
  Refer to the option `scope` in `nslcd.conf(5)` for the meaning of these values.
  Optional.
* `nslcd_user_filter`, `nslcd_group_filter`  
  An LDAP search filter for user and groups lookups, respectively.
  If not set, nslcd does a basic search on the object class.
  Optional.
* `nslcd_extra_options`  
  A dictionary of further options to set in `nslcd.conf`.
  Dictionary keys are options; values are option values.
  Optional.
* `nslcd_mkhomedir`  
  Enable the `mkhomedir` PAM module, which handles creation of user home directories on login.
  Defaults to `false`.
* `nslcd_extra_groups`  
  A list of groups that the nslcd system user is added to.
  This allows granting access to additional resources, such as the private key file.
  All groups need to exist on the target system; this role does not create them.
  Empty by default.
* `nslcd_inaccessible_paths`  
  If the target system uses systemd, this option takes a list of paths, that should not be accessible at all for nslcd.
  Regardless of this option, home directories are made inaccessible and the rest of the file system is mostly read-only.
  Optional.

Dependencies
------------

This role does not set up TLS client certificates and therefore depends on a role that generates and deploys them, if TLS client authentication is desired.

Example Configuration
---------------------

The following is a short example for some of the configuration options this role provides:

```yaml
nslcd_uri:
  - 'ldaps://ldap.example.org'
  - 'ldaps://ldap-fallback.example.org'
nslcd_binddn: 'cn=some_user,ou=machines,dc=my,dc=domain'
nslcd_bindpw: 'some_password'
nslcd_base: 'dc=my,dc=domain'
nslcd_user_base: 'ou=people,{{ nslcd_base }}'
nslcd_group_base: 'ou=groups,{{ nslcd_base }}'
nslcd_extra_options:
  nss_min_uid: 2000
nslcd_mkhomedir: true
```

License
-------

MIT
