# IonicNotification

> _Work in progress, gem is not yet on RubyGems, it will be there as soon as it is production ready. Should be a matter of days and be online 8 Dec._

IonicNotification is a Ruby gem that provides an interface to use [Ionic.io](http://ionic.io/)'s [Push Notification](http://docs.ionic.io/docs/push-overview) service.

_Keep in mind that Ionic.io's Push Notification is still in alpha at the moment, so this gem will probably undergo some major updates in the near future._

## Getting Started

```
gem install ionic_push_notification
```

You can add IonicNotification to your Gemfile with:

```
gem 'ionic_notification'
```

After you installed IonicNotification, you need to run the generator:

```
rails g ionic_notification:install
```

Configure the ionic_notification.rb file within config/initializers. You can use the default set environment variables, change them or hardcode those values (altough is highly NOT recommended)

```Ruby
  IonicNotification.setup do |config|
    # ==> Configuration for the Ionic.io Application ID
    # The Application ID can be found on the dashboard of
    # https://apps.ionic.io/apps
    config.ionic_application_id = ENV["IONIC_APPLICATION_ID"]

    # ==> Configuration for the Private API Key
    # The Private API Key for your application can be found
    # within the Settings of your application on
    # https://apps.ionic.io/apps
    config.ionic_api_key = ENV["IONIC_API_KEY"]

    # Your Ionic app name will be used for the notification
    # title if none is provided. If you leave this undefined
    # IonicNotification will use your Rails app name
    config.ionic_app_name = "YourAppName"

    # ==> Configuration for the location of the API
    # Refer to the Ionic documentation for the correct location
    # Current documentation can be found here:
    # http://docs.ionic.io/docs/push-sending-push and
    # defaults to https://push.ionic.io
    # config.ionic_api_url = ENV["IONIC_API_URL"]
  end
```

## Use from within your Model
If you want to use IonicNotification from within your ActiveRecord model you can run the proper generator:
```
rails g ionic_notification:model YOUR_MODEL
```
This creates a migration that will add a `:device_tokens` column to your model
```Ruby
class AddDeviceTokensTo_YOUR_MODEL < ActiveRecord::Migration
  def self.up
    add_column :your_model_table_name, :device_tokens, :text
  end

  def down
    remove_column :your_model_table_name, :device_tokens
  end
end
```

and includes IonicNotification in your model file
```Ruby
# app/models/your_model.rb
class YourModel < ActiveRecord::Base
  # Include IonicNotification behaviour
  include IonicNotification::Concerns::IonicNotificable

  # Your stuff...
end
```

## Sending a Push Notification

```Ruby
# Create an array of device tokens you want to send to
device_tokens = [
  "APA91bEBoyoZ3EDXJbdjvzn2jdikRu7tdpz_65zkqfMDFTSNZfNgg-ohiNYQQ1TCTdjwqWZ",
  "WSt1XklxZeVj1WtxVBZXMIpIEt3YVaDs3f2Hp5QVmkwB0QgJ48d6KHVrHZCJxTER52yK3b0"
]

# Create a PushService instance for sending notifications
# The constructor can take a hash of parameters including:
#   device_tokens - An array of device tokens to push to
#   message - a hash the represents the message to send  
service = IonicNotification::PushService.new(device_tokens: device_tokens)

# Call the notify! method to send the push notification
# The following sends sends a message that looks like this:
# {
#   "tokens":[
#    "APA91bEBoyoZ3EDXJbdjvzn2jdikRu7tdpz_65zkqfMDFTSNZfNgg-ohiNYQQ1TCTdjwqWZ",
#    "WSt1XklxZeVj1WtxVBZXMIpIEt3YVaDs3f2Hp5QVmkwB0QgJ48d6KHVrHZCJxTER52yK3b0"
#   ],
#   "notification":{
#     "alert":"Is this you John Wayne? Is this me?",
#   }
# }
service.alert!("Is this you John Wayne? Is this me?")

# You can also use the notify method and provide a message in a block
# The following sends sends a message that looks like this:
# {
#   "tokens":[
#    "APA91bEBoyoZ3EDXJbdjvzn2jdikRu7tdpz_65zkqfMDFTSNZfNgg-ohiNYQQ1TCTdjwqWZ",
#    "WSt1XklxZeVj1WtxVBZXMIpIEt3YVaDs3f2Hp5QVmkwB0QgJ48d6KHVrHZCJxTER52yK3b0"
#   ],
#   "notification":{
#     "alert":"You've got updated messages!",
#     "android":{
#       "title":"Updated Messages",
#       "payload":{
#         "type":"configuration"
#       }
#     }
#   }
# }
service.notify do
  {
    "alert":"You've got updated messages!",
    "android":{
      "title":"Updated Messages",
      "payload":{
        "type":"configuration"
      }
    }
  }
end
```

## License

This project uses MIT-LICENSE.
This project started from the codebase of [nwwatson _ionic_push_repo_](https://github.com/nwwatson/ionic_push)