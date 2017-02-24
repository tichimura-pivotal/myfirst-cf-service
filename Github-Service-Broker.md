curlでgithubのtokenを取得

```
$ curl -u tichimura-pivotal -d '{"scopes": ["repo", "delete_repo"], "note": "CF Service Broker"}' https://api.github.com/authorizations
Enter host password for user 'tichimura-pivotal':
{
  "id": 84535006,
  "url": "https://api.github.com/authorizations/84535006",
  "app": {
    "name": "CF Service Broker",
    "url": "https://developer.github.com/v3/oauth_authorizations/",
    "client_id": "00000000000000000000"
  },
  "token": "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
  "hashed_token":   "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
  "token_last_eight": "883815f2",
  "note": "CF Service Broker",
  "note_url": null,
  "created_at": "2017-02-21T17:52:32Z",
  "updated_at": "2017-02-21T17:52:32Z",
  "scopes": [
    "repo",
    "delete_repo"
  ],
  "fingerprint": null
}

```

jpxxichimt1m2:service_broker ichimt1$ vi Gemfile

```
source 'https://rubygems.org'
ruby "2.1.8"

```

自由に変更

```
services: # catalog must advertise at least one service
  - id: 5085f1cb-e093-4630-8795-843b76018eb8
    name: github-repo
    plans: # a service has one or more plans
      - id: c05855e7-a55d-4ba1-b462-7579df7514f4
```


以下の行を追加（local repoでのemailの確認）

```
"cd /tmp/#{repo_name} && git config user.email 'vcap@pcf.local' 2>&1",
```

```
jpxxichimt1m2:service_broker ichimt1$ cf push github-broker-ichi
Updating app github-broker-ichi in org Demo / space demo as admin...
OK

Uploading github-broker-ichi...
Uploading app files from: /Users/ichimt1/git/github-service-broker-ruby/service_broker
Uploading 4.8K, 10 files
Done uploading
OK

Stopping app github-broker-ichi in org Demo / space demo as admin...
OK

Starting app github-broker-ichi in org Demo / space demo as admin...
Downloading dotnet_core_buildpack...
Downloading java_buildpack_offline...
Downloading staticfile_buildpack...
Downloading nodejs_buildpack...
Downloading go_buildpack...
Downloaded nodejs_buildpack
Downloading python_buildpack...
Downloaded staticfile_buildpack
Downloaded dotnet_core_buildpack
Downloading dotnet-core-buildpack...
Downloading php_buildpack...
Downloaded java_buildpack_offline
Downloading binary_buildpack...
Downloaded go_buildpack
Downloading ruby_buildpack...
Downloaded php_buildpack
Downloaded dotnet-core-buildpack
Downloaded ruby_buildpack
Downloaded python_buildpack
Downloaded binary_buildpack
Creating container
Successfully created container
Downloading app package...
Downloaded app package (5K)
Downloading build artifacts cache...
Downloaded build artifacts cache (2.1M)
Staging...
-------> Buildpack version 1.6.28
       Downloaded [file:///tmp/buildpacks/4d448968dd3a37095ee2fca995f23507/dependencies/https___buildpacks.cloudfoundry.org_concourse-binaries_bundler_bundler-1.13.6.tgz]
-----> Compiling Ruby/Rack
       Downloaded [file:///tmp/buildpacks/4d448968dd3a37095ee2fca995f23507/dependencies/https___buildpacks.cloudfoundry.org_concourse-binaries_ruby_ruby-2.1.8-linux-x64.tgz]
-----> Using Ruby version: ruby-2.1.8
-----> Installing dependencies using bundler 1.13.6
       Running: bundle install --without development:test --path vendor/bundle --binstubs vendor/bundle/bin -j4 --deployment
       Fetching gem metadata from https://rubygems.org/........
       Fetching version metadata from https://rubygems.org/..
       Fetching dependency metadata from https://rubygems.org/.
       Using rake 10.1.1
       Using addressable 2.3.5
       Using multipart-post 1.2.0
       Using json 1.8.1
       Using rack 1.5.2
       Using tilt 1.4.1
       Using sshkey 1.6.1
       Using bundler 1.13.6
       Using faraday 0.8.8
       Using rack-protection 1.5.1
       Using sinatra 1.4.4
       Using sawyer 0.5.1
       Using octokit 2.6.3
       Bundle complete! 10 Gemfile dependencies, 13 gems now installed.
       Gems in the groups development and test were not installed.
       Bundled gems are installed into ./vendor/bundle.
       Bundle completed (1.99s)
       Cleaning up the bundler cache.
-----> Writing config/database.yml to read from DATABASE_URL
-----> Detecting rake tasks
Exit status 0
Staging complete
Uploading droplet, build artifacts cache...
Uploading build artifacts cache...
Uploading droplet...
Uploaded build artifacts cache (2.1M)
Uploaded droplet (17.3M)
Uploading complete
Destroying container
Successfully destroyed container

1 of 1 instances running

App started


OK

App github-broker-ichi was started using this command `bundle exec rackup config.ru -p $PORT`

Showing health and status for app github-broker-ichi in org Demo / space demo as admin...
OK

requested state: started
instances: 1/1
usage: 1G x 1 instances
urls: github-broker-ichi.cfapps.haas-64.pez.pivotal.io
last uploaded: Tue Feb 21 18:17:25 UTC 2017
stack: cflinuxfs2
buildpack: ruby 1.6.28

     state     since                    cpu    memory        disk          details
#0   running   2017-02-22 03:17:52 AM   0.0%   31.9M of 1G   72.8M of 1G

jpxxichimt1m2:service_broker ichimt1$ cf create-service-broker github-did admin password http://github-broker-ichi.cfapps.haas-64.pez.pivotal.io
Creating service broker github-did as admin...
OK
jpxxichimt1m2:service_broker ichimt1$ cf service-access
Getting service access as admin...
broker: app-autoscaler
   service          plan     access   orgs
   app-autoscaler   bronze   all
   app-autoscaler   gold     all

broker: p-rabbitmq
   service      plan       access   orgs
   p-rabbitmq   standard   all

broker: p-mysql
   service   plan    access   orgs
   p-mysql   100mb   all
   p-mysql   200mb   none

broker: p-spring-cloud-services
   service                       plan       access   orgs
   p-circuit-breaker-dashboard   standard   all
   p-config-server               standard   all
   p-service-registry            standard   all

broker: apigee-cf-service-broker
   service       plan           access   orgs
   apigee-edge   org            all
   apigee-edge   microgateway   all

broker: github-did
   service       plan     access   orgs
   github-repo   public   none
```

```
jpxxichimt1m2:service_broker ichimt1$ cf enable-service-access -p public -o Demo github-repo
Enabling access to plan public of service github-repo for org Demo as admin...
OK
```

```
jpxxichimt1m2:example_app ichimt1$ vi Gemfile
jpxxichimt1m2:example_app ichimt1$ cf push github-consumer-ichi
jpxxichimt1m2:service_broker ichimt1$ cf create-service github-repo public github-repo-1
Creating service instance github-repo-1 in org Demo / space demo as admin...
OK
jpxxichimt1m2:service_broker ichimt1$ cf bind-service github-consumer-ichi github-repo-1
Binding service github-repo-1 to app github-consumer-ichi in org Demo / space demo as admin...
OK
TIP: Use 'cf restage github-consumer-ichi' to ensure your env variable changes take effect
jpxxichimt1m2:service_broker ichimt1$ cf service github-repo-1

Service instance: github-repo-1
Service: github-repo
Bound apps: github-consumer-ichi
Tags:
Plan: public
Description: Provides read and write access to a GitHub repository.
Documentation url: https://github.com/cloudfoundry-samples/github-service-broker-ruby
Dashboard: https://github.com/tichimura-pivotal/github-service-ede226a0-322b-4195-8e11-53d6a0b32651

Last Operation
Status: create succeeded
Message:
Started: 2017-02-21T18:33:04Z
Updated: 2017-02-21T18:33:04Z

```
