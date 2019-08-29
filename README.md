# Golang Webcounter Vagrant Box

This is a Packer project to build a Vagrant box with the [webcounter](https://github.com/slavrd/go-redis-counter/tree/master/webcounter) application installed as service.

## Prerequisites

* Install [Packer](https://www.packer.io/downloads.html).
* Install [Vagrant](https://www.vagrantup.com/downloads.html).
* Ruby version ~> 2.5.1 for running KitchenCI test.

## Building the box with Packer

The packer template is in `template.json`. In the `variables` section you can set parameters to customize the build. Help on setting, overriding variables in packer can be found [here](https://www.packer.io/docs/templates/user-variables.html#setting-variables).

* `base_box`  - the base box to use. It needs to be a box published to Vagrant cloud so named `user/box`
* `skip_add` - weather to skip adding the base box to vagrant. If the box is not already added packer will fail.
* `build_name` - used internally to set parameters of the packer builder. Changing it will change the path of the output artifact, so you will need to adjust parameter in other files like the `box_url` in `.kitchen.yml`.

Run `packer validate template.json` - to make basic template validation.

Run `packer build template.json` - to build the Vagrant box with packer.

## Testing with KitchenCI

The project includes a KitchenCI configuration to run basic tests against the box outputted from packer.

To run it you need to install the gems specified in the `Gemfile`. Its recommended to use ruby [`bundler`](https://bundler.io/).

Installing gems with bundler:

* `gem install bundler`
* `bundle install`

Running Kitchen tests:

* `bundle exec kitchen converge` - will build the test environment.
* `bundle exec kitchen verify` - will run the tests.
* `bundle exec kitchen destroy` - will destroy the test environment.
* `bundle exec kitchen test` - will perform the above steps with a single command.

## Uploading to Vagrant cloud

The project includes a script `scripts\vagrant_cloud_upload.sh` to upload the box to Vagrant cloud. It is basically a wrapper for the `vagrant cloud publish` [command](https://www.vagrantup.com/docs/cli/cloud.html#cloud-publish) so it can be used instead.

You need to set up Vagrant cloud access by setting the `VAGRANT_CLOUD_TOKEN` environment variable to your user token.

script usage: `scripts\vagrant_cloud_upload.sh [box_version]`, box_version will default to `yy.mm.dd` if not set.

Inside the script there are other variables to define the Vagrant cloud box.

## Example

```bash
packer validate template.json # basic template validation

packer build -var 'skip_add=false' template.json   # build the box with Packer, setting the skip_add variable so the base box will be added to Vagrant.

bundle exec kitchen test # run tests

scripts/vagrant_cloud_upload.sh 0.6.1 # upload to Vagrant cloud 
```