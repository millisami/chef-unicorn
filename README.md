Unicorn Cookbook
=========

[Unicorn][1] Unicorn is an HTTP server for Rack applications designed to only serve fast clients on low-latency, high-bandwidth connections and take advantage of features in Unix/Unix-like kernels. Slow clients should only be served by placing a reverse proxy capable of fully buffering both the the request and response in between Unicorn and slow clients.

Overview
--------

This Cookbook will install Unicorn and provide a decent basis for usage with a rack/rails application with some attention made to bundler.

Usage
--------

* Add the Unicorn to your [Gemfile][2] this is mandatory if you want downtime free deploys to work correctly.  If you do not use [Bundler][3] you can ignore this warning.

* If you use [Bundler][3] you should configure the gem_home variable.  This should be a predictable path provided to bundler upon installing your bundle with the --path argument.
>``bundle install --path /data/example/bundled_gems``
 * You would then configure gem_home like this:

```ruby
node.default[:unicorn][:gem_home] = '/data/example/bunled_gems'
```

* Your application recipe should look something like this:

```ruby
include_recipe "unicorn"
node.default[:unicorn][:worker_timeout] = 180  
node.default[:unicorn][:preload_app] = false  
node.default[:unicorn][:worker_processes] = 4 
node.default[:unicorn][:before_fork] = 'sleep 1'  
node.default[:unicorn][:port] = 8080
node.default[:unicorn][:gem_home] = nil
node.default[:unicorn][:stderr_path] = 'log/stderr.log'  
node.default[:unicorn][:stdout_path] = 'log/stdout.log'  
node.default[:unicorn][:logger] = 'log/unicorn.log'
node.set[:unicorn][:options] = { :tcp_nodelay => true, :backlog => 4096 }

unicorn_config "/etc/unicorn/example.rb" do
  listen({ node[:unicorn][:port] => node[:unicorn][:options] })  
  working_directory '/data/example/current'
  worker_timeout node[:unicorn][:worker_timeout]  
  gem_home node[:unicorn][:gem_home]
  preload_app node[:unicorn][:preload_app]
  worker_processes node[:unicorn][:worker_processes]  
  before_fork node[:unicorn][:before_fork]   
  logger node[:unicorn][:logger]
  stderr_path node[:unicorn][:stderr_path]  
  stdout_path node[:unicorn][:stdout_path] 
end
```


Authors and Copyright
--------

Author:: Scott M. Likens <scott@likens.us>
Author:: Adam Jacob <adam@opscode.com>

Copyright 2009-2010, Opscode, Inc.

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.

[1]: http://unicorn.bogomips.org/
[2]: http://gembundler.com/gemfile.html
[3]: http://gembundler.com/
