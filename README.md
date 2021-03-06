# Demo application to show Puma leaks

This is a skeleton Rails 4.2.6 application that demonstrates an occasional leak in the Puma application server.

To reproduce:
  - Clone this repository
  - `bundle install`
  - `bundle exec puma -t 0:16 -e production config.ru`
  - Every 5 seconds, the number of `Puma::Client` and `ActionController::Base` objects is printed. This number should be steady at 0 when the server is at rest.
  - In another window, run `siege -c 10 -r 50 -v http://localhost:9292` (may need several times to trigger the leak).

Expected: Both counts should revert to 0 after the siege ends.

Result: Sometimes, both counts are stuck at 1, which means that a Puma::Client and associated controller object has leaked.

Tested on OSX 10.10.5 with Ruby 2.2.5 and Rails 4.2.6.

Sample output: (it took two runs of `siege` here, sometimes it takes fewer or more)

```
Mor% bundle exec puma -t 0:16 -e production config.ru
Puma starting in single mode...
* Version 3.4.0 (ruby 2.2.5-319), codename: Owl Bowl Brawl
* Min threads: 0, max threads: 16
* Environment: production
Puma clients: 0
Controllers:  0
* Listening on tcp://0.0.0.0:9292
Use Ctrl-C to stop
Puma clients: 10
Controllers:  10
Puma clients: 4
Controllers:  4
Puma clients: 3
Controllers:  3
Puma clients: 3
Controllers:  3
Puma clients: 3
Controllers:  3
Puma clients: 1
Controllers:  1
Puma clients: 0
Controllers:  0
Puma clients: 6
Controllers:  6
Puma clients: 4
Controllers:  4
Puma clients: 7
Controllers:  7
Puma clients: 3
Controllers:  3
Puma clients: 4
Controllers:  4
Puma clients: 2
Controllers:  2
Puma clients: 1
Controllers:  1
Puma clients: 1
Controllers:  1
Puma clients: 1
Controllers:  1
Puma clients: 1
Controllers:  1
```
