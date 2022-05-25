# Ansible Role: Image Uploader (for Openstack Glance)

An ansible role that keeps your images up to date.

## Disclaimer

Never run unchecked code on your production systems!

This role (currently) requires admin permissions to change image visibility!

While we're trying to keep this role as safe as possible, mistakes can happen.

As such, we need to emphasize:

>  15. Disclaimer of Warranty.
>
>  THERE IS NO WARRANTY FOR THE PROGRAM, TO THE EXTENT PERMITTED BY
APPLICABLE LAW.  EXCEPT WHEN OTHERWISE STATED IN WRITING THE COPYRIGHT
HOLDERS AND/OR OTHER PARTIES PROVIDE THE PROGRAM "AS IS" WITHOUT WARRANTY
OF ANY KIND, EITHER EXPRESSED OR IMPLIED, INCLUDING, BUT NOT LIMITED TO,
THE IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS FOR A PARTICULAR
PURPOSE.  THE ENTIRE RISK AS TO THE QUALITY AND PERFORMANCE OF THE PROGRAM
IS WITH YOU.  SHOULD THE PROGRAM PROVE DEFECTIVE, YOU ASSUME THE COST OF
ALL NECESSARY SERVICING, REPAIR OR CORRECTION.

>  16. Limitation of Liability.
>
>  IN NO EVENT UNLESS REQUIRED BY APPLICABLE LAW OR AGREED TO IN WRITING
WILL ANY COPYRIGHT HOLDER, OR ANY OTHER PARTY WHO MODIFIES AND/OR CONVEYS
THE PROGRAM AS PERMITTED ABOVE, BE LIABLE TO YOU FOR DAMAGES, INCLUDING ANY
GENERAL, SPECIAL, INCIDENTAL OR CONSEQUENTIAL DAMAGES ARISING OUT OF THE
USE OR INABILITY TO USE THE PROGRAM (INCLUDING BUT NOT LIMITED TO LOSS OF
DATA OR DATA BEING RENDERED INACCURATE OR LOSSES SUSTAINED BY YOU OR THIRD
PARTIES OR A FAILURE OF THE PROGRAM TO OPERATE WITH ANY OTHER PROGRAMS),
EVEN IF SUCH HOLDER OR OTHER PARTY HAS BEEN ADVISED OF THE POSSIBILITY OF
SUCH DAMAGES.

## Requirements

  * [Openstack SDK](https://docs.openstack.org/openstacksdk/latest/user/install.html) below version 0.99
  * Configured Openstack credentials (preferrably by using a `clouds.yaml` file and the corresponding `OS_CLOUD` environment variable).
  * Admin privileges to allow setting image visibility.

## Role variables

```yaml
images_enabled:
  - ubuntu/xenial_xerus
  - ubuntu/jammy_jellyfish
  - ubuntu/bionic_beaver
  - ubuntu/focal_fossa
  - debian/bullseye
  - debian/buster
  - flatcar/production
```

This list is used to load image configurations from `vars/images/`, either for preconfigured images from this role or for your own images inside your ansible project directory.

```yaml
images_cleanup: false
```

Removes **unused** (no existing instance specifying this image), **non-latest** images from glance.
This is independent from any previous action.

```yaml
images_testing: true

images_testing_auto_ip: true
images_testing_availability_zone: es1
images_testing_boot_from_volume: false
images_testing_delete_fip: true
images_testing_flavor: m1.small
```

Defines if images should run through a basic testing before making them available.
If testing is enabled (default), your cloud details need to be adapted.
During each test, a complete set containing a router, network, subnet, security groups and instance will be spawned to make sure the client operating system can be reached, logged into and used.

---
Each image configuration has a set of parameters:

```yaml
distribution:     Name of the distribution (required)
release:          Release/Codename to get (required)
url:              Url to latest image file (required)
checksum:         Url to checksum file, detects version changes (required)
algorithm:        Algoritm used for the checksum (required)
version:          Version number (optional)
ssh_user:         Default user in the image (optional, defaults to distribution)
testing_enabled:  Enables extended testing (optional, defaults to True)
display_name:     Override the generated image name (optional)
# (more to come)
```

This list will change based on new image requirements. We recommend keeping your copy of this role close to latest.

## Use with ansible

Create a playbook with the following content:

```yaml
- name: Upload images
  hosts: localhost
  tasks:
    - name: Upload images
      include_role:
        name: image_uploader
      vars:
        images_enabled:
          - ubuntu/jammy_jellyfish
          - my/fancy_image
```

You can specify undefined image configurations, ansible will tell you where to place the configuration file on running.

You can also add your own reporting, for example to slack, by adding instructions to the same play. The results (image name and uuid) are stored in the `images_uploaded` variable.

## Use in a ci pipeline

Create a job in your pipeline, providing an ansible-enabled environment, (encrypted) openstack credentials and this role through ansible galaxy and you're good to go.

## Participate

We're happy to add your image definitions, as long as they are useful for others, and testable.

This role will be extended with more features over time.

## About the authors

This role was written by 2 members (@tkearney and @max06) of our OpenStack team to make our own life much easier. It worked well enough for us so we decided to share it with you.
