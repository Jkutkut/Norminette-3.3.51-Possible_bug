# Posible bug en Norminette:

## Description:

Crash when checking .h files with invalid header protection and define as the last instruction inside the if statement.

	$> norminette include/doesnt_work_incorrect.h
	Traceback (most recent call last):
	File "/usr/local/bin/norminette", line 8, in <module>
		sys.exit(main())
	File "/Library/Python/3.7/site-packages/norminette/__main__.py", line 121, in main
		registry.run(context, source)
	File "/Library/Python/3.7/site-packages/norminette/registry.py", line 65, in run
		ret, jump = self.run_rules(context, rule)
	File "/Library/Python/3.7/site-packages/norminette/registry.py", line 43, in run_rules
		self.run_rules(context, self.rules[r])
	File "/Library/Python/3.7/site-packages/norminette/registry.py", line 32, in run_rules
		rule.run(context)
	File "/Library/Python/3.7/site-packages/norminette/rules/check_preprocessor_define.py", line 127, in run
		context.new_error("HEADER_PROT_ALL_2", context.peek_token(i))
	File "/Library/Python/3.7/site-packages/norminette/context.py", line 204, in new_error
		self.errors.append(NormError(errno, tkn.pos[0], tkn.pos[1]))
	AttributeError: 'NoneType' object has no attribute 'pos'

## Example:
With the files in the directory ``include/`` we can see where *norminette* crashes and what should happend:
- If we execute:

		norminette include/works_correct.h

	o any of this commands

		norminette include/works_incorrect_01.h
		norminette include/works_incorrect_02.h
		norminette include/works_incorrect_03.h
	
	we can see that *norminette* works as spected (detecting if the header protection is valid or not).

- If we execute now:

		norminette include/doesnt_work_incorrect.h

	we can see that the program crashes (giving us the error at the top of the document).

<br>

## Results:
I believe there is an error when checking if the header protection if the last instruction of the if statement is a ``# define`` (see ``norminette include/works_incorrect_03.h``). It should work the same way as the rest of the cases and not end unspectedly.

## Details:
- This error may be fixed already with [#273](https://github.com/42School/norminette/commit/29eff0a90a1e4843f4689e9ccc8a06a4743b87a6) because it changes a line related with the error. However, I am not able to test this theory (public release is not updated with this change and I am not able to run norminette cloning the repository).
- Tested versions:
	- Mac OS (42Madrid):
		- norminette 3.3.51
		- python 3.7.3
	- Ubuntu 21.10:
		- norminette 3.3.51
		- python 3.9.2
	- Debian GNU/Linux 11.
		- norminette 3.3.51
		- python 3.9.2
