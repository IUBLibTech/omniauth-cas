# OmniAuth IU CAS Strategy [![Gem Version][version_badge]][version] [![Build Status][travis_status]][travis]

[version_badge]: https://badge.fury.io/rb/omniauth-iu-cas.png
[version]: http://badge.fury.io/rb/omniauth-iu-cas
[travis]: https://travis-ci.org/IUBLibTech/omniauth-cas
[travis_status]: https://secure.travis-ci.org/IUBLibTech/omniauth-cas.png
[releases]: https://github.com/IUBLibTech/omniauth-cas/releases
[upstream]: https://github.com/dlindahl/omniauth-cas
[iu_login]: https://cas.iu.edu
[kb_app_code]: https://kb.iu.edu/d/alqm

This is a OmniAuth 1.0 compatible port of the previously available
[OmniAuth CAS strategy][old_omniauth_cas] that was bundled with OmniAuth 0.3.

It is forked from the original [omniauth-cas][upstream] and has been customized for use with [IU Login][iu_login].

* [View the documentation][document_up]
* [Changelog][releases]

## Installation

Add this line to your application's Gemfile:

    gem 'omniauth-cas'

And then execute:

    $ bundle

Or install it yourself as:

    $ gem install omniauth-cas

## Usage

Use like any other OmniAuth strategy:

```ruby
Rails.application.config.middleware.use OmniAuth::Builder do
  provider :cas, host: 'cas.yourdomain.com'
end
```

### Configuration Options

#### Required

OmniAuth CAS requires at least one of the following two configuration options:

  * `url` - Defines the URL of your CAS server (i.e. `http://example.org:8080`)
  * `host` - Defines the host of your CAS server (i.e. `example.org`).

#### Optional

Other configuration options:

  * `port` - The port to use for your configured CAS `host`. Optional if using `url`.
  * `ssl` - TRUE to connect to your CAS server over SSL. Optional if using `url`.
  * `service_validate_url` - The URL to use to validate a user. Defaults to `'/serviceValidate'`.
  * `callback_url` - The URL custom URL path which CAS uses to call back to the service.  Defaults to `/users/auth/cas/callback`.
  * `logout_url` - The URL to use to logout a user. Defaults to `'/logout'`.
  * `login_url` - Defines the URL used to prompt users for their login information. Defaults to `/login` If no `host` is configured, the host application's domain will be used.
  * `uid_field` - The user data attribute to use as your user's unique identifier. Defaults to `'user'` (which usually contains the user's login name).
  * `ca_path` - Optional when `ssl` is `true`. Sets path of a CA certification directory. See [Net::HTTP][net_http] for more details.
  * `disable_ssl_verification` - Optional when `ssl` is true. Disables verification.
  * `on_single_sign_out` - Optional. Callback used when a [CAS 3.1 Single Sign Out][sso]
    request is received.
  * `cassvc` - Optional custom IU option. Set to pass an [application code][kb_app_code] to CAS.
  * `fetch_raw_info` - Optional. Callback used to return additional "raw" user
    info from other sources.

    ```ruby
    provider :cas,
      fetch_raw_info: Proc.new { |strategy, opts, ticket, user_info, rawxml|
        return {} if user_info.empty? || rawxml.nil? # Auth failed

        extra_info = ExternalService.get(user_info[:user]).attributes
        extra_info.merge!({'roles' => rawxml.xpath('//cas:roles').map(&:text)})
        extra_info
      }
    ```

Configurable options for values returned by CAS:

  * `uid_key` - The user ID data attribute to use as your user's unique identifier. Defaults to `'user'` (which usually contains the user's login name).
  * `name_key` - The data attribute containing user first and last name.  Defaults to `'name'`.
  * `email_key` - The data attribute containing user email address.  Defaults to `'email'`.
  * `nickname_key` - The data attribute containing user's nickname.  Defaults to `'user'`.
  * `first_name_key` - The data attribute containing user first name.  Defaults to `'first_name'`.
  * `last_name_key` - The data attribute containing user last name.  Defaults to `'last_name'`.
  * `location_key` - The data attribute containing user location/address.  Defaults to `'location'`.
  * `image_key` - The data attribute containing user image/picture.  Defaults to `'image'`.
  * `phone_key` - The data attribute containing user contact phone number.  Defaults to `'phone'`.

## Migrating from OmniAuth 0.3

Given the following OmniAuth 0.3 configuration:

```ruby
provider :CAS, cas_server: 'https://cas.example.com/cas/'
```

Your new settings should look similar to this:

```ruby
provider :cas,
         host:      'cas.example.com',
         login_url: '/cas/login',
  	     service_validate_url: '/cas/serviceValidate'
```

If you encounter problems wih SSL certificates you may want to set the `ca_path` parameter or activate `disable_ssl_verification` (not recommended).

## Contributing

1. Fork it
2. Create your feature branch (`git checkout -b my-new-feature`)
3. Commit your changes (`git commit -am 'Added some feature'`)
4. Push to the branch (`git push origin my-new-feature`)
5. Create new Pull Request

## Thanks

Special thanks go out to the following people

  * Phillip Aldridge (@iterateNZ) and JB Barth (@jbbarth) for helping out with Issue #3
  * Elber Ribeiro (@dynaum) for Ubuntu SSL configuration support
  * @rbq for README updates and OmniAuth 0.3 migration guide

[old_omniauth_cas]: https://github.com/intridea/omniauth/blob/0-3-stable/oa-enterprise/lib/omniauth/strategies/cas.rb
[document_up]: http://dlindahl.github.com/omniauth-cas/
[net_http]: http://ruby-doc.org/stdlib-1.9.3/libdoc/net/http/rdoc/Net/HTTP.html
[sso]: https://wiki.jasig.org/display/CASUM/Single+Sign+Out
