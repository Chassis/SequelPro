# Sequel Pro for Chassis

Adds a handy `vagrant sequel` command to simplify access to your database.


## Installation

We recommend [installing this extension globally](http://docs.chassis.io/en/latest/extend/#globally-installing-extensions) to make it available on every Chassis box.

```
git clone https://github.com/Chassis/SequelPro ~/.chassis/extensions/sequelpro
```

Alternatively to install this extension into a single Chassis box, add `chassis/sequelpro` to your `extensions` list in your `config.local.yaml`:

```yaml
extensions:
  - chassis/sequelpro
```

(For manual installation in an individual Chassis box, clone this repository into that Chassis box's `extensions` directory.)


## Usage

Once the virtual machine is running, run `vagrant sequel` within the Chassis folder to open the VM's databases in Sequel Pro.

## Configuration

The SequelPro extension allows for custom configuration options which can be added in one of your [configuration files](https://docs.chassis.io/en/latest/config/). Here's an example configuration:

```yaml
# MySQL database details.
database:
  name: your_database
  user: your_username
  password: your_password
  prefix: bq_
ssh:
  host: 10.1.2.3
  user: custom_user
  port: 33060
  private_key_path:
    - /Users/username/.vagrant.d/boxes/chassis-VAGRANTSLASH-chassis/3.0.3/virtualbox/vagrant_private_key
```

Any property you omit within the `ssh:` section will fall back to a default value, so you may specify for example only "port" or "user" without filling out every value.

If your configuration file specifies a custom `ip:`, that value will be used as the default for the `ssh.host` property.

```yaml
# Example simplified configuration:
# Use the default database values, a custom IP, and port 22
ip: 10.86.73.15

ssh:
  port: 22
```

## Connection Errors

If you get a connection error, the first thing to attempt to debug is to check the details that Sequel Pro gives you (under the Show Details button).


### `key_load_public: No such file or directory`

If you get this error on macOS Sierra, it's possible that you have too many SSH keys loaded into your `ssh-agent`. If you're using multiple Chassis boxes with [this setup in your SSH config](http://apple.stackexchange.com/a/264974/55070) (`AddKeysToAgent yes`), each new box you add will be added to your agent. With too many of these, SSH will hit the authentication retries limit before getting to the correct key.

The simple solution is to add this to your `~/.ssh/config` file:

```
# Disable checks on Vagrant machines
Host 127.0.0.1
	# Skip adding to agent
	AddKeysToAgent no

	# Only use key specified on CLI
	IdentitiesOnly yes

	# Skip known hosts
	UserKnownHostsFile /dev/null
	StrictHostKeyChecking no
```

This disables using system-level keys (both from the agent, and your regular SSH keys), and disables host checks (which are not necessary for `localhost`). This does not affect `vagrant ssh`, which already uses these options.
