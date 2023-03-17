# Item 41: Be Aware Of IRB's Advanced Features

* IRB has concept of sessions, which is like a copy of IRB inside IRB
  * Each new sessions has it's own evaluation context so setting local variables in one session won't impact another session
* Can "zoom" into an object in a session, making it the current evaluation context and effectively changing the object `self` references
* `irb` command can be used to create a new session and if given an object as an argument, the `irb` command will start a new session and make that object the new evaluation context (the `self` variable)
* Each session in IRB has a unique ID to help manage all running sessions
  * Use `jobs` command to list all sessions
  * `fg` command will make the session with the matching ID the active session
  * Can use `exit` command to terminate active session or `kill` command to terminate session with specific ID
  
```bash
irb> self.class
# Object

irb> irg [1, 2, 3]

irb#1> self.class
# Array

irb#1> length
# 3
```