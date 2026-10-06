Challenge: Login Admin (SQL Injection)
Difficulty: ★★☆☆☆

Category: Injection

Status: Solved
What the challenge asks :
Log in with the administrator account of the shop.


Steps I followed :
1)Opened the Login page

I went to the normal login form (Account → Login).
Tried a basic single quote to test for SQL Injection;

In the Email field I typed just a single quote:" ' "to see whether special SQL syntax characters are being handled safely or are able to disturb the application's SQL query
Then I entered any password and clicked Log in.=> The app showed a weird error ([object Object]).
This is a sign that the application is vulnerable to SQL Injection (it didn’t handle the quote properly).
2)Used the classic SQL Injection payload

Following the built-in hint, I changed the Email field to:' OR true--
*The single quote (') closes the original query early (will explain it more later)
*OR true (or OR 1=1) makes the condition always true.
*-- comments out the rest of the query
Logged in successfully

3)After submitting the form with the payload above, I was logged in as the administrator 
(admin@juice-sh.op)
Green success banner appeared.

further explanation(most important one):
The login form builds a SQL query like this behind the scenes:
SQLSELECT * FROM Users WHERE email = 'what-you-typed' AND password = '...'
When you type ' OR true--, the query becomes:
SQLSELECT * FROM Users WHERE email = '' OR true--' AND password = '...'
Because of OR true, it always returns a user (the first one, which is the admin).
