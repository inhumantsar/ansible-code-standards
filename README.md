# Ansible Code Standards

This is a living document meant to describe the basic state for all of Ansible code. This can refer to one-off playbooks for individual hosts, or tidy reusable roles published to the Ansible Galaxy. It's meant to guide development toward practices which make everyone's lives easier in the long run. Anything here which doesn't contribute to that goal should be argued about and revised.

## The Golden Rule: Keep it simple

## Minimal Standards

### Tested
  * Syntax checked (eg: `ansible-playbook test.yml --syntax-check`)
  * [Asserts](http://docs.ansible.com/ansible/assert_module.html) added alongside tasks, which are then...
  * Implemented in a dummy environment which resembles real life.
    * eg: Apply role or playbook to a RHEL7 Docker container

### Consistent
  * Linted (eg: `ansible-lint /path/to/somerole`)
    * In CI but also on the local dev host.
    * Integrates with [Atom](https://atom.io/packages/linter-ansible-linting), and [add IDE here].
  * Use Ansible for everything in a playbook or role unless there is an excellent reason not to.
    * eg: Don't shell out to a bash script kept with the role when everything in the bash script can be expressed in Ansible tasks.
  * Don't mix styles (tabs and spaces, line endings, indentation levels, etc.) in the same project.
  * If working on someone else's code, adopt their style.
  * When in doubt, the world can always live with the following:
    * 4 spaces for each indentation level
    * UTF-8 for file encoding
    * LF (Unix-style) line endings

### Documented
  * Engineers have to be able to read and make sense of your code without reading the README.
  * Users have to able to run your code with only the README.
  * Code should be obvious in intent, self-documenting.
  * Echoes to the log or screen are often good replacements for code comments.
    * This is why Ansible has a freetext "name" field for each task which is echoed to the screen *and* logs.

### Sanitized
  * No unencrypted secrets ever for any reason.
  * No sensitive IP addresses, URLs, personal or corporate information ever for any reason.
  * No log files, temp files, one-off helper scripts, etc. (`.gitignore` is your friend)

### Reviewed
  * The first commit to a new project should be a merge request handed to a co-worker.
  * Nothing ends up in a release without having been approved by a co-worker.
  * Reviewers have three key responsibilities:
    1. Ensure these requirements are met.
    2. Ensure that they understand the intent of the project and the intent of the change.
    3. Ensure that no failed test represents a potentially blocking issue.

## Roles

Roles are meant to be co-exist in an ecosystem of roles, whether they're published publicly or not. They have a few extra requirements which help reduce duplicated effort and help encourage reusability.

### Instantiation

```bash
pip install cookiecutter
cookiecutter https://github.com/inhumantsar/cookiecutter-ansible-role
```

### Standards
#### Scoped Appropriately
  * Only do what *needs* doing.
  * Avoid installing the entire stack of pre-requisites whenever possible.
    * eg: Roles which launch Java applications should not attempt to install Java.
    * Users often have specific requirements when it comes to pre-requisites, let them choose how to meet them.
  * Call on public roles to do the heavy lifting when required.
    * eg: Roles which launch ancient Java applications with very specific requirements may *need* to call on a Java role to install an application-specific Java.

#### Parameterized
  * If it might change, it should probably be a parameter.
    * Package names, URLs, port numbers, usernames, passwords, version numbers, etc.
  * Include sane values for all required parameters in `defaults/main.yml`.
    * Users should be able to run the role without parameters and get a positive (if limited) result.

#### Prepped for the Galaxy
  * A `LICENSE` file must exist and include a valid copyright license. (GPL, BSD, proprietary, etc.)
  * `meta/main.yml` needs to be filled in, tags and all.
  * `requirements.yml` needs to be accurate and up-to-date.
  * There cannot be any proprietary information in the role.
    * This is what parameters and secrets management systems are for.
    * If the role absolutely cannot be released to the Galaxy:
      1. Document an explanation in the README.
      2. The LICENSE file *must* reflect that. See below.

```
Copyright (C) Acme Systems, Inc.  All Rights Reserved.

Proprietary and confidential. Unauthorized copying of this file via any medium
is strictly prohibited.

Written by Elmer Fudd <efudd@acmesystems.com>, September 1943.
```

## Playbooks

Playbooks describe specific, end-of-line configurations. For example, we might have
written roles to deploy an Nginx/PHP server, and another to deploy a PostgreSQL DB.
We'll put those together, adding different parameters and inventories for each environment, and
use them to set up a specific web application; for example, the company's Wordpress site.

### Instantiation

```bash
pip install cookiecutter
cookiecutter https://github.com/inhumantsar/cookiecutter-ansible-playbook
```

### Standards
#### Individualized
  * Generally, one repo should contain one playbook which accomplishes one goal.
    * eg: The `ansible-fooapp` repo contains the `fooapp` playbook which configures the `fooapp` service on particular hosts.
  * Multiple playbooks are reasonable to keep in one repo if they are all necessary to acheive the
same end goal.
    * eg: `fooapp` requires its own cluster of `memcached` servers, so having a `memcached` playbook in the same repo would make sense.

#### Complete
  * Nothing should require tailoring prior to use.
  * Programmatically fetch or generate anything external.

#### Sanitized
  * _This is here again because playbooks are the most immediately logical place to put secrets._
  * No unencrypted secrets ever for any reason.
  * No sensitive IP addresses, URLs, personal or corporate information ever for any reason. (These are lesser secrets, but still secrets.)
  * No log files, temp files, one-off helper scripts, etc. (`.gitignore` is your friend)
