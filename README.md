# Ansible Role: Image Uploader (for Openstack Glance)

An ansible role that keeps your images up to date.

## Disclaimer

Never run unchecked code on your production systems!

This role (currently) requires admin permissions to change image visibility!

While we're trying to keep this role as safe as possible, mistakes can happen.

As such, we need to emphasize:

>   7. Disclaimer of Warranty. Unless required by applicable law or
>      agreed to in writing, Licensor provides the Work (and each
>      Contributor provides its Contributions) on an "AS IS" BASIS,
>      WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or
>      implied, including, without limitation, any warranties or conditions
>      of TITLE, NON-INFRINGEMENT, MERCHANTABILITY, or FITNESS FOR A
>      PARTICULAR PURPOSE. You are solely responsible for determining the
>      appropriateness of using or redistributing the Work and assume any
>      risks associated with Your exercise of permissions under this License.

>   8. Limitation of Liability. In no event and under no legal theory,
>      whether in tort (including negligence), contract, or otherwise,
>      unless required by applicable law (such as deliberate and grossly
>      negligent acts) or agreed to in writing, shall any Contributor be
>      liable to You for damages, including any direct, indirect, special,
>      incidental, or consequential damages of any character arising as a
>      result of this License or out of the use or inability to use the
>      Work (including but not limited to damages for loss of goodwill,
>      work stoppage, computer failure or malfunction, or any and all
>      other commercial damages or losses), even if such Contributor
>      has been advised of the possibility of such damages.

## Requirements

  * [Openstack SDK](https://docs.openstack.org/openstacksdk/latest/install/index.html) below version 0.99
    * `pip install openstacksdk==0.61.0`
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
checksum_raw:     Raw checksum, if hardcoded in the image definition (optional, only if checksum is not set)
algorithm:        Algoritm used for the checksum (required)
version:          Version number (optional)
ssh_user:         Default user in the image (optional, defaults to distribution)
testing_enabled:  Enables extended testing (optional, defaults to True)
display_name:     Override the generated image name (optional)
min_disk_size:    Override the minimum disk size (optional, defaults to 10GB)
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
