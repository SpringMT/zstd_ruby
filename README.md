[![Gem Version](https://badge.fury.io/rb/zstd-ruby.svg)](https://badge.fury.io/rb/zstd-ruby)
![Build Status](https://github.com/SpringMT/zstd-ruby/actions/workflows/ruby.yml/badge.svg?branch=main)

# zstd-ruby

Ruby binding for zstd (Zstandard), a fast real-time compression algorithm

For more information about the algorithm:

- https://github.com/facebook/zstd
- https://en.wikipedia.org/wiki/Zstd

## Zstd version

This gem is currently using the [v1.5.6](https://github.com/facebook/zstd/tree/v1.5.6) version of zstd.

## Installation

Add this line to your Gemfile:

```ruby
gem 'zstd-ruby'
```

And then execute:

```sh
$ bundle install
```

## Usage

```ruby
require 'zstd-ruby'
```

### Compression

#### Simple Compression

```ruby
compressed_data = Zstd.compress(data)
compressed_data = Zstd.compress(data, level: complession_level) # default compression_level is 3
```

#### Compression with Dictionary
```ruby
# dictionary is supposed to have been created using `zstd --train`
compressed_using_dict = Zstd.compress("", dict: File.read('dictionary_file'))
```

#### Streaming Compression
```ruby
stream = Zstd::StreamingCompress.new
stream << "abc" << "def"
res = stream.flush
stream << "ghi"
res << stream.finish
```

or

```ruby
stream = Zstd::StreamingCompress.new
res = stream.compress("abc")
res << stream.flush
res << stream.compress("def")
res << stream.finish
```

#### Streaming Compression with Dictionary
```ruby
stream = Zstd::StreamingCompress.new(dict: File.read('dictionary_file'))
stream << "abc" << "def"
res = stream.flush
stream << "ghi"
res << stream.finish
```

#### Streaming Compression with level and Dictionary
```ruby
stream = Zstd::StreamingCompress.new(level: 5, dict: File.read('dictionary_file'))
stream << "abc" << "def"
res = stream.flush
stream << "ghi"
res << stream.finish
```

### Decompression

#### Simple Decompression

```ruby
data = Zstd.decompress(compressed_data)
```

#### Decompression with Dictionary
```ruby
# dictionary is supposed to have been created using `zstd --train`
Zstd.decompress(compressed_using_dict, dict: File.read('dictionary_file'))
```

#### Streaming Decompression
```ruby
cstr = "" # Compressed data
stream = Zstd::StreamingDecompress.new
result = ''
result << stream.decompress(cstr[0, 10])
result << stream.decompress(cstr[10..-1])
```

#### Streaming Decompression with dictionary
```ruby
cstr = "" # Compressed data
stream = Zstd::StreamingDecompress.new(dict: File.read('dictionary_file'))
result = ''
result << stream.decompress(cstr[0, 10])
result << stream.decompress(cstr[10..-1])
```

### Skippable frame

```ruby
compressed_data_with_skippable_frame = Zstd.write_skippable_frame(compressed_data, "sample data")

Zstd.read_skippable_frame(compressed_data_with_skippable_frame)
# => "sample data"
```

### Stream Writer and Reader Wrapper
**EXPERIMENTAL**

* These features are experimental and may be subject to API changes in future releases.
* There may be performance and compatibility issues, so extensive testing is required before production use.
* If you have any questions, encounter bugs, or have suggestions, please report them via [GitHub issues](https://github.com/SpringMT/zstd-ruby/issues).

#### Zstd::StreamWriter

```ruby
require 'stringio'
require 'zstd-ruby'

io = StringIO.new
stream = Zstd::StreamWriter.new(io)
stream.write("abc")
stream.finish

io.rewind
# Retrieve the compressed data
compressed_data = io.read
```

#### Zstd::StreamReader

```ruby
require 'stringio'
require 'zstd-ruby' # Add the appropriate require statement if necessary

io = StringIO.new(compressed_data)
reader = Zstd::StreamReader.new(io)

# Read and output the decompressed data
puts reader.read(10)  # 'abc'
puts reader.read(10)  # 'def'
puts reader.read(10)  # '' (end of data)
```

## JRuby

This gem does not support JRuby.

Please consider using https://github.com/luben/zstd-jni.

## Development

After checking out the repo, run `bin/setup` to install dependencies. Then, run `rake spec` to run the tests. You can also run `bin/console` for an interactive prompt that will allow you to experiment.

To install this gem onto your local machine, run `bundle exec rake install`. To release a new version, update the version number in `version.rb`, and then run `bundle exec rake release`, which will create a git tag for the version, push git commits and tags, and push the `.gem` file to [rubygems.org](https://rubygems.org).

## Contributing

Bug reports and pull requests are welcome on GitHub at https://github.com/SpringMT/zstd-ruby. This project is intended to be a safe, welcoming space for collaboration, and contributors are expected to adhere to the [Contributor Covenant](http://contributor-covenant.org) code of conduct.

## Credits

This library was originally forked off from https://github.com/jarredholman/ruby-zstd.

## License

The gem is available as open source under the terms of the [BSD-3-Clause License](https://opensource.org/licenses/BSD-3-Clause).

