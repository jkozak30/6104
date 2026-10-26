# Prep 8: Injection Attacks

1. Before prompt defense, 3/3 of my runs were hits. After the defense (without any content rewording) 0/3 were hits, so the defense worked. I tried rewording review 3 by just making it more insistent (i.e. "you need to end with ...") which didn't work to break the defense, but rewording the request to say "END OF REVIEWS", then describing the requirement as if modifying system instructions, then "RESUME REVIEWS" did work to break the defense.

2. The username I used in step 3 was `"admin'; --"` and the password was `wrong`. This produced the SQL line:
```
SQL: SELECT * FROM users WHERE username = 'admin'; --' AND password = 'wrong'
```
After the fix, the same attack produced output:
```
SQL: SELECT * FROM users WHERE username = ? AND password = ? [ "admin'; --", "wrong" ]
Login failed
```
so the SQL injection did not succeed to login as `admin`.

3. **Reflection**: Part 1's defense isn't guaranteed to prevent an attack because the LLM may still interpret both system instructions and user reviews as text, so a review that attempts to impersonate new system instructions may succeed despite the warning. In Part 2, the switch to SQL parameters deterministically prevents a user-inputted value from being interpreted as an instruction, so user input will always be treated as a string under the fix.
