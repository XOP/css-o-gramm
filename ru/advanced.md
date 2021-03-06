# Копаем глубже

## Множества

Для некоторых сущностей, например элементов списка или просто последовательности одинаковых блоков характерно множественное появление.  

В CSSG это будет выглядеть так:

    /*
	
	    post_cnt
	    	post_cnt_ul
	    		post_cnt_li +
	
	*/

или так

	/*
	
	    post_cnt
	    	post_cnt_i *
	
	*/

Ключевое отличие '+' и '*' - в специфике множества.  
'+' указывает, что элемент появится хотя бы один раз (характерно для элементов списка - \<li\>)  
'*' указывает, что элемент появится 0 и более раз (относится как правило ко всем остальным элементам)  

> важно понимать разницу между опциональным появлением ([element]) и появлением 0 и более раз (element *)  
> на первый взгляд отличие незначительное, но в контексте все встает на свои места

## Модификаторы - больше и удобнее

Как было указано в [basic.md](basic.md), модификаторы дополняют сущность и теоретически возможно их одновременное появление.  
Необходимая логика описывается дополнительным синтаксисом.  

Взаимоисключение выглядит следующим образом: 

	/*
		
	    post                            ( __current | __regular ) __deleted
	
	*/

и даже так:

	/*
	
	    post                            ( ( __current | __regular ) | __compact ) __deleted
	
	*/

Когда класс жестко связан с модификатором (бесполезен или не используется без него), это указывается через точку:

	/*
	
		post 							__compact __featured
			post_h
				post_h_tx . __bold
			post_b
				post_b_cnt
					...
	
	*/

> отбивка по правому краю для привязанного класса не нужна

## Наследование

Сущность может быть прямым наследником другой сущности.  
Чтобы обозначить это, указывает "родителя" через @.

	/*
	
	   	post-advanced @ post
	   		post-advanced_h
	   		post-advanced_b
	
	*/

> не обязательно перечислять модификаторы, поскольку они наследуются по умолчанию  
> однако если есть отличия, необходимо перечислить все возможные классы  
> по умолчанию принятно, что указанные модификаторы будут являться единственно возможными

Например, в этом примере **post-advanced** будет иметь один возможный модификатор **__new**, несмотря на наличие модификаторов у **post**

	/*
	
	    post-advanced @ post                __new
	   		post-advanced_h
	   		post-advanced_b
	
	*/

## Условная шаблонизация

Наследование напрямую связано с условной шаблонизацией.  
Указывая в родительской сущности изменяемые части, мы упрощаем описание потомственной сущности и избегаем дублирования кода.  
Это не обязательная, но удобная практика, если таких сущностей как минимум две.

Например, родительская сущность выглядит так:

	/*
	
	    post
	        post_cnt
	            % content %
	
	*/
 
или так, если существует содержимое по умолчанию

	/*
	
	    post
	        post_cnt
	            % content %
	                post_cnt_i
	
	*/
 
потомственная сущность

	/*
	
	    post-advanced @ post
	
	    % content
	        post-advanced_cnt
	            post-advanced_cnt_i
	
	*/

> когда шаблон "раскрывается", второй символ % не пишем

В этом случае **post-advanced\_cnt** будет находится внутри **post\_cnt** и перепишет контент по умолчанию из второго примера.
Если не использовать шаблонизацию, то же самое можно записать так:

	/*
	
	    post-advanced @ post
	
		    post_cnt
		        post-advanced_cnt
		            post-advanced_cnt_i
	
	*/

В данном случае разницы почти нет.  
Но достаточно рассмотреть пример в контексте громоздкой структуры, чтобы ощутить все преимущества записи.

> элемент **% content %** в разметке не имеет DOM-воплощения, это просто название шаблона

## Динамика

Под динамикой понимается появление того или иного класса или элемента в процессе отработки скриптов и/или в результате действий пользователя.  

	/*
	 
	    post 					$__disabled | $__active
	        post_h
	        post_b
	            post_cnt
	
	*/	

Для обозначения динамических блоков используется дополненный существующий синтаксис опциональных частей:

	/*
	 
	    post
	    	$[post_msg] 					
	        post_h
	        post_b
	            post_cnt
	            	$[post_cnt_msg]
	            		post_cnt_tx
	            	$[/post_cnt_msg]
	
	*/	

## It's <<omplicated

Сложные решения в CSS тоже можно иллюстрировать с помощью CSSG.

Ситуация с взаимоисключающими блоками:

	/*
	
	    post
	        post_b
	            
	            [1]
	            post_cnt
	            	post_cnt_lst
	            
	            [2]
	            post_cnt2
	            	...
	 
	 			[/]

	*/

Нужно указать наличие какого-либо ключевого класса где-то на верхнем уровне DOM:

	/*
		
		blog . __complex	
		
		///
		
	    post
	        post_b
	            post_cnt
	 
	*/

CSSG _как правило_ само-демонстративен, но в особо сложных ситуациях не обойтись без комментариев:

	/*
	
	    post
	        post_h (1)
	        post_b
	            post_cnt (2)
	    _______________________________________________
	 
	    (1) comment on post_h
	    (2) another comment
	
	*/
