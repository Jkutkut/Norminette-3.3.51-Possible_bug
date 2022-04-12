# Posible bug en Norminette:

## Descripción:

El posible bug ocurre en archivos .h al tener una protección de header inválida y un define como última instrucción dentro del if. Esto hace que norminette tenga un crash.

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

## Ejemplo:
En los archivos adjuntos en el directorio ``include/``, se muestra un ejemplo de cómo falla norminette y qué debería ocurrir:
- Si ejecutamos:

		norminette include/works_correct.h

	o cualquiera de estos

		norminette include/works_incorrect_01.h
		norminette include/works_incorrect_02.h
		norminette include/works_incorrect_03.h
	
	vemos que el *norminette* funciona como es esperado (diciendo si el header es válido o no).

- Vemos que falla en los incorrectos debido a que falta en la protección de cada header ``_H`` al final.

- Sin embargo, si ejecutamos:

		norminette include/doesnt_work_incorrect.h

	vemos que nos salta el error mostrado en el inicio.

<br>

## Conclusión:

Creo que existe un fallo en caso en el que la protección del header es incorrecta y la última instrucción del .h es un ``# define`` (ver ``norminette include/works_incorrect_03.h``). Debería funcionar de igual manera y no terminar de manera inesperada.

## Detalles:
- Versiones testeadas:
	- Mac: norminette 3.3.51
	- Linus: norminette 3.3.51