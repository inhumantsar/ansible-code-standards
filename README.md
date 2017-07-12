# Ansible Code Standards

This is a living document meant to describe the basic state for all of Ansible code. This can refer to one-off playbooks for individual hosts, or tidy reusable roles published to the Ansible Galaxy. It's meant to guide development toward practices which make everyone's lives easier in the long run. Anything here which doesn't contribute to that goal should be argued about and revised.

## First and always: Keep It Simple and Specific

## Naming Conventions

`ansible-<type>-<infra>-<application>`

Where _type_ could be either `role` or `play`, _infra_ would be an OS or cloud provider
or a system and _application_ would be the particular component being configured.

The key goal here is clarity. The `ansible` prefix makes the repo easy to group and search for. Being explicit about whether it's a `play` or `role` helps set up some immediate expectations about how the code operates. Identifying the target infrastructure helps keep different implementations of the same thing separate, as well as communicate potential compatibility issues instantly.

Note that if you submit a role to the Galaxy prefixed with `ansible-`, they'll strip that off the role name and also give you the opportunity to set it manually. So `ansible-role-docker-compose-gitlab` could be referred to in `requirements.yml` files as `<username>.docker-compose-gitlab`.

#### Examples
* `ansible-role-docker-compose-gitlab` - This one might be too long, could probably drop the `compose`.
* `ansible-role-rhel-selinux`
* `ansible-role-linux-sshconfig` - _linux_ used instead of listing specific distros
* `ansible-play-labjenkins-javaslaves`
* `ansible-play-aws-ecs-artifactory`


## Minimal Standards

Every point below comes with an asterisk implied: _\* Within reason._&nbsp; Sometimes hardcoding data (especially Ansible inventories) is the only reasonable option. Sometimes you need to merge a change whose tests aren't _all_ passing. Exceptional circumstances.

### Tested
  1. Syntax checked with `ansible-playbook --syntax-check`.
  1. Detailed [asserts](http://docs.ansible.com/ansible/assert_module.html) written for each task.
  1. Implemented in a dummy environment which resembles live environments.

### Consistent
  1. Linted with [ansible-lint](https://github.com/willthames/ansible-lint).
  1. Avoids shelling out to a script if the work can be done with Ansible tasks.
  1. Uses the same indentation, encoding, and line endings throughout the project.

### Documented
  1. Engineers can read the code without consulting the README.
  1. Users can run your code consulting _only_ the README.
  1. Is obvious in intent, self-documenting.
  1. Produces log messages before and after long-running, complex, or risky actions.

### Sanitized
  1. No unencrypted secrets in any repo for any reason, ever.
  1. No sensitive technical, personal, or corporate information in public repos, ever. <sup>(try to avoid it in private repos too!)</sup>

### Reviewed
  1. Merge/pull requests for every change to shared branches.
  1. Nothing gets merged without having been reviewed by a co-worker.
  1. Reviewers have three key responsibilities:
    * _Ensure these requirements are met._
    * _Ensure that they understand the intent of the project and the intent of the change._
    * _Ensure that no failed test represents a potentially blocking issue._

## Roles

Roles are meant to be co-exist in an ecosystem of roles, whether they're published publicly or not. They have a few extra requirements which help reduce duplicated effort and help encourage reusability.

### Scoped Appropriately
  1. Avoids installing the entire stack of pre-requisites whenever possible.
    * eg: Roles which launch Java applications should not attempt to install Java.
  2. Call on public roles to do work whenever possible.
    * eg: Roles which launch ancient Java applications with very specific requirements may *need* to call on a Java role to install an application-specific Java.

### Parameterized
  1. Avoids hardcoding configuration data.
    * Package names, URLs, port numbers, usernames, passwords, version numbers, etc.
  2. Includes sane values for all required parameters in `defaults/main.yml`.

### Prepped for the Galaxy
  1. Includes a `LICENSE` file (see below).
  1. Includes the relevant information in `meta/main.yml`.
  1. Includes an accurate and up-to-date `requirements.yml`.

```
Copyright (C) Acme Systems, Inc.  All Rights Reserved.

Proprietary and confidential. Unauthorized copying of this file via any medium
is strictly prohibited.

Written by Elmer Fudd <efudd@acmesystems.com>, September 1943.
```

## Playbooks

Where Roles describe a generic unit of configuration Playbooks describe specific implementations. They are responsible for the end configuration of particular systems, eg: a Kubernetes cluster, or a Master/Slave database combo.

### Individualized
  1. Contains only the playbooks it needs to accomplish its intent.

### Complete
  1. Requires no manual tailoring prior to use.
  1. Programmatically fetches or generates anything dynamic.

# Resources
## Instantiation
### Playbooks
  ```bash
  pip install cookiecutter
  cookiecutter https://github.com/inhumantsar/cookiecutter-ansible-playbook
  ```

### Roles
  ```bash
  pip install cookiecutter
  cookiecutter https://github.com/inhumantsar/cookiecutter-ansible-role
  ```
