[![Bitdeli Badge](https://d2weczhvl823v0.cloudfront.net/cstrahan/capnp-ruby/trend.png)](https://bitdeli.com/free "Bitdeli Badge")

![Cap'n Proto][logo]

# Ruby Edition

This here is a [Ruby][ruby] wrapper for the official C++ implementation of [Cap'n Proto][capnp].

[![Build Status][travis-badge]][travis-link]

# Installing

First [install libcapnp][libcapnp-install], then install the gem:

```bash
gem install capn_proto --pre
```

The native extension for this gem requires a C++ compiler with C++11 features, so use the same C++ compiler and flags that you used to compile libcapnp (e.g. `CXX` and `CXXFLAGS`). As an OSX user, having followed the [instructions for installing libcapnp on OSX][libcapnp-install], the correct incantation is as follows:

```bash
CXX=$HOME/clang-3.2/bin/clang++ gem install capn_proto --pre
```

# Example

```ruby
require 'capn_proto'

module AddressBook extend CapnProto::SchemaLoader
  load_schema("addressbook.capnp")
end

def write_address_book(file)
  addresses = AddressBook::AddressBook.new_message
  people = addresses.initPeople(2)

  alice = people[0]
  alice.id = 123
  alice.name = 'Alice'
  alice.email = 'alice@example.com'
  alice_phones = alice.initPhones(1)
  alice_phones[0].number = "555-1212"
  alice_phones[0].type = 'mobile'
  alice.employment.school = "MIT"

  bob = people[1]
  bob.id = 456
  bob.name = 'Bob'
  bob.email = 'bob@example.com'
  bob_phones = bob.initPhones(2)
  bob_phones[0].number = "555-4567"
  bob_phones[0].type = 'home'
  bob_phones[1].number = "555-7654"
  bob_phones[1].type = 'work'
  bob.employment.unemployed = nil

  addresses.write(file)
end

def print_address_book(file)
  addresses = AddressBook::AddressBook.read_from(file)

  addresses.people.each do |person|
    puts "#{person.name} : #{person.email}"

    person.phones.each do |phone|
      puts "#{phone.type} : #{phone.number}"
    end

    if person.employment.unemployed?
      puts "unemployed"
    if person.employment.employer?
      puts "employer: #{person.employment.employer}"
    if person.employment.school?
      puts "student at: #{person.employment.school}"
    if person.employment.selfEmployed?
      puts "self employed"
    end
  end
end

if __FILE__ == $0
  file = File.open("addressbook.bin", "wb")
  write_address_book(file)

  file = File.open("addressbook.bin", "rb")
  print_address_book(file)
end
```

# Status

What's implemented:
- Schema parsing/loading
- Message reading
  - From byte string
  - From file descriptor
- Message writing
  - To byte string
  - To file descriptor

What's to come:
- More reading/writing mechanisms:
  - Packing/unpacking
- Extensive test coverage
- Proper support for [JRuby][jruby]
- Support for RPC

[logo]: https://raw.github.com/cstrahan/capnp-ruby/master/media/captain_proto_small.png "Cap'n Proto"
[ruby]: http://www.ruby-lang.org/ "Ruby"
[capnp]: http://kentonv.github.io/capnproto/ "Cap'n Proto"
[jruby]: http://jruby.org/ "JRuby"
[libcapnp-install]: http://kentonv.github.io/capnproto/install.html "Installing Cap'n Proto"
[mit-license]: http://opensource.org/licenses/MIT "MIT License"

[travis-link]: https://travis-ci.org/cstrahan/capnp-ruby
[travis-badge]: https://travis-ci.org/cstrahan/capnp-ruby.png?branch=master
