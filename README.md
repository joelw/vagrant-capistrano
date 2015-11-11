# Vagrant::Capistrano

This vagrant-plugin allows you to call capistrano from vagrant. Useful in combination with the puppet provisioner. 

## Installation

```
vagrant plugin install vagrant-capistrano
```

## Usage

    config.vm.provision "capistrano" do |cap|
      cap.capfile = '../some-project/Capfile'
      cap.rubystring = 'ruby-2.0.0-p458@gemset' 
      cap.stage = 'testing'
      cap.tasks = ['rvm:install'] 
      cap.environment = { 'ENV_VAR' => 'VALUE' } 
      cap.hiera_root = '../hiera' 
    end

The correct rubystring can be found using rvm current.
This provisioner will set the following environment variables before executing capistrano:

    DEPLOYMENT_USER = 'vagrant' (or whatever the vagrant ssh user is set to)
    SSH_IDENTITY = private ssh key of the DEPLOYMENT_USER
    HOSTS= IP address of the vagrant ssh daemon
    SSH_PORT= Port of the vagrant ssh daemon

In order for this plugin to successfully execute your capsitrano tasks, should your include the following in your Capfile (or deploy.rb):

    # required for vagrant
    set :ssh_options,   { keys: [ENV['SSH_IDENTITY']] } if ENV['SSH_IDENTITY']
    set :user,          ENV['DEPLOYMENT_USER'] || "deployment"

In your deployment stage file:

    server ENV['HOSTS'], user: ENV['DEPLOYMENT_USER'], roles: %w(web app db refresh_view), port: ENV['SSH_PORT']

This provisioner will cd into the directory containing your Capfile, then perform the tasks listed in the cap.tasks array. If
omitted, it defaults to:

    cap (stage) misc:update_needed? rvm:install_ruby deploy:setup deploy
    cap (stage) rvm:install_ruby
    cap (stage) deploy:setup
    cap (stage) deploy

## Contributing

1. Fork it ( http://github.com/artcom/vagrant-capistrano/fork )
2. Create your feature branch (`git checkout -b my-new-feature`)
3. Commit your changes (`git commit -am 'Add some feature'`)
4. Push to the branch (`git push origin my-new-feature`)
5. Create new Pull Request
