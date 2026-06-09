- Summary:
    Citizen Portal Login failure after update
- Impact:
    Login partially unavailable for 27 minutes leading to frustration and disapointment with the user
    Around 1200 failed login attempts
    Citizens could not submit online requests
    internal back-office work was delayed leading to higher work pressure and delay on other dependent work
    reputation damage and trust issues
- Timeline
  09:05 Monitoring detected a sharp increase in login errors 
  09:10 Support started receiving user calls
  09:18 DevOps found errors in the authentication service
  09:25 team rolled back to the previous version
  09:32 login service recovered
  
- Likely root cause:
  an error in validating user credentials due to a change in the code

  
- 3 action items with owners
  1- Providing help for users (Support team)
  2- Error Detection (DevOps team)
  3- RollBack to past version (DevOps team)
 
- 1 lesson for the next sprint
    Additional testing for the login process in future updates
    Avoid udate deployment at peak usage time or shortly befor that
    Prioritize rolling back to stable running version over finding the errors when the service is down
