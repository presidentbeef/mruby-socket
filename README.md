mruby-socket
============

"mruby-socket" mrbgem provides BSD socket interface for mruby.
API is compatible with CRuby's "socket" library.

## Installation
Add the line below to your `build_config.rb`:

```ruby
  conf.gem :github => 'mruby-esp32/mruby-socket', :branch => 'esp32'
```

If stack overflow occurs, increase the stack size

+ mruby_task: 8192 => 32768

  `xTaskCreate()` in `main/mruby_main.c`

+ eventTask: 4096 => 32768

  ```
  $ make menuconfig
  Component config ---> ESP32-specific ---> Event loop task stack size
  ```
  
## Example
```ruby
puts "Getting ready to start Wi-Fi"

wifi = ESP32::WiFi.new

wifi.on_connected do |ip|
  puts "Wi-Fi Connected: #{ip} (#{Socket.gethostname})"
  soc = TCPSocket.open("www.kame.net", 80)
  msg = "HEAD / HTTP/1.1\r\nHost: www.klab.com\r\nConnection: close\r\n\r\n"
  msg.split("\r\n").each do |e|
    puts ">>> #{e}"
  end
  soc.send(msg, 0)
  puts "--------------------------------------------------------------------------------"
  loop do
      buf = soc.recv(128, 0)
      break if buf.length == 0
      print buf
  end
  puts ""
  puts "--------------------------------------------------------------------------------"
end

wifi.on_disconnected do
  puts "Wi-Fi Disconnected"
end

puts "Connecting to Wi-Fi"
wifi.connect('SSID', 'PASSWORD')

#
# Loop forever otherwise the script ends
#
while true do
  ESP32::System.delay(1000)
end
```

## Requirement
- [mruby-esp32/mruby-io](https://github.com/mruby-esp32/mruby-io) mrbgem
- [iij/mruby-pack](https://github.com/iij/mruby-pack) mrgbem
- system must have RFC3493 basic socket interface
- and some POSIX API...

## TODO
- add missing methods
- fix possible descriptor leakage (see XXX comments)


## License

Copyright (c) 2013 Internet Initiative Japan Inc.

Permission is hereby granted, free of charge, to any person obtaining a 
copy of this software and associated documentation files (the "Software"), 
to deal in the Software without restriction, including without limitation 
the rights to use, copy, modify, merge, publish, distribute, sublicense, 
and/or sell copies of the Software, and to permit persons to whom the 
Software is furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in 
all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR 
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, 
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE 
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER 
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING 
FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER 
DEALINGS IN THE SOFTWARE.
