# Ansible Role: Image Uploader (for Openstack Glance)

An ansible role that keeps your images up to date.

## Requirements

  * [Openstack SDK](https://docs.openstack.org/openstacksdk/latest/user/install.html)
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
