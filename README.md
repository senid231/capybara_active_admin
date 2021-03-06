# Capybara Active Admin

Capybara DSL for fast and easy testing Active Admin applications.

## Installation

Add this line to your application's Gemfile:

```ruby
group :test do
  gem 'capybara_active_admin'
end
```

And then execute:

    $ bundle install

Or install it yourself as:

    $ gem install capybara_active_admin

## Usage

`rails_helper.rb`
```ruby
require 'capybara/active_admin/rspec'
```

`spec/system/users_spec.rb`
```ruby
RSpec.describe 'Users', js: true do
  subject do
    visit admin_users_path
  end

  let!(:john) { User.create!(full_name: 'John Doe') }
  let!(:jane) { User.create!(full_name: 'Jane Air') }

  it 'have john and jane in users table' do
    subject

    expect(page).to have_action_item('New User')
    expect(page).to_not have_action_item('Edit User')

    with_table_for('users') do
      expect(page).to have_table_row(count: 2)
      expect(page).to have_table_col('John Doe')
      expect(page).to have_table_col('John Doe', row_id: john.id)
      expect(page).to have_table_col('John Doe', row_id: john.id, col_name: 'Full Name')

      expect(page).to_not have_table_col('John Doe', row_id: john.id, col_name: 'Id')
      expect(page).to_not have_table_col('John Doe', row_id: jane.id)
      expect(page).to_not have_table_col('John Doe', row_id: jane.id, col_name: 'Full Name')
    end
  end

  it 'creates user' do
    subject

    click_action_item('New User')
    expect(page).to have_current_path(new_admin_user_path)

    fill_in 'Full name', with: 'Johny Cage'
    click_button 'Create User'
    expect(page).to have_flash_message('User was successfully created.', type: :notice)
    user = User.last!
    expect(page).to have_current_path admin_user_path(user.id)

    expect(User.count).to eq(1)
    expect(user).to have_attributes(full_name: 'Johny Cage')
  end
end
```

See `spec/support` for more user examples.
See `capybara/active_admin/test_helpers.rb` for available DSL methods.

## Development

After checking out the repo, run `bin/setup` to install dependencies. Then, run `rake spec` to run the tests. You can also run `bin/console` for an interactive prompt that will allow you to experiment.

To install this gem onto your local machine, run `bundle exec rake install`. To release a new version, update the version number in `version.rb`, and then run `bundle exec rake release`, which will create a git tag for the version, push git commits and tags, and push the `.gem` file to [rubygems.org](https://rubygems.org).

## Contributing

Bug reports and pull requests are welcome on GitHub at https://github.com/activeadmin-plugins/capybara_active_admin. This project is intended to be a safe, welcoming space for collaboration, and contributors are expected to adhere to the [code of conduct](https://github.com/activeadmin-plugins/capybara_active_admin/blob/master/CODE_OF_CONDUCT.md).

## License

The gem is available as open source under the terms of the [MIT License](https://opensource.org/licenses/MIT).

## Code of Conduct

Everyone interacting in the Capybara::ActiveAdmin project's codebases, issue trackers, chat rooms and mailing lists is expected to follow the [code of conduct](https://github.com/activeadmin-plugins/capybara_active_admin/blob/master/CODE_OF_CONDUCT.md).
