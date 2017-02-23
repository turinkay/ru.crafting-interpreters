^title Введение
^part Добро пожаловать

> Сказки – больше, чем правда, не потому, что в них рассказывается о существовании драконов, а потому, что они говорят нам: драконов можно победить.”
>
> <cite>Нил Гейман</cite>

Я очень рад, что мы пройдем этот путь вместе. Эта книга о реализации 
интерпретаторов для языков программирования. Также она о том, как спроектировать
язык, достойный реализации. Мне бы хотелось, чтобы эта книга была у меня в то 
время, когда я только начал погружение в языки, и я пишу её в своей <span
name="head">голове</span> в течение почти десятка лет.

<aside name="head">

Мои друзья и семья, простите за то, что я был так погружен в собственные мысли!

</aside>

На этих страницах мы последовательно рассмотрим два полных интерпретатора для 
полноценного языка. Полагаю, что это ваше первое погружение в языки, поэтому 
постараюсь покрыть каждую концепцию и строчку кода, которые нужны вам чтобы 
построить полную, практичную и быструю реализацию языка программирования.

Чтобы уместить две реализации в одну книгу и не сделать её такой большой, что ей
можно было бы подпирать двери, текст содержит меньше теории, чем в другой 
подобной литературе. По мере того как мы будем выстраивать части системы, я буду
рассказывать истории и концепции, которые за ними стоят. Я постараюсь 
познакомить вас с профессиональным жаргоном, так что если вы попадете на <span 
name="party">коктейльную вечеринку</span>, полную исследователей ЯП (языков 
программирования), то впишетесь в неё.

<aside name="party">

Довольно странно, но я оказывался в такой ситуации несколько раз. Вы не 
поверите, сколько некоторые из них могут выпить.

</aside>

Но мы в основном будем тратить наши мозговые ресурсы на то, чтобы язык 
запустился и заработал. Это не значит, что теория не важна. Умение рассуждать 
четко и <span name="formal">формально</span>  о синтаксисе и семантике – 
несомненно важное умение при работе с языком. Но лично я изучаю что-либо гораздо
лучше в процессе работы. Мне сложно пройти через множество параграфов, полных 
абстрактных понятий, и действительно понять их. Только когда я что-то 
запрограммировал, запустил и отладил, я могу сказать, что действительно 
*понимаю* это.

<aside name="formal">

Статические системы типов особенно требуют строгого формального обсуждения. 
Разработка системы типов схожа с доказательством теоремы в математике.

Оказывается, это не случайно. В первой половине прошлого века Хаскелл Карри и 
Уильям Алвин Говард разработали доказательство того, что это стороны одной 
монеты: [изоморфизм Карри-Говарда][the curry-howard isomorphism].

[the curry-howard isomorphism]: https://ru.wikipedia.org/wiki/%D0%A1%D0%BE%D0%BE%D1%82%D0%B2%D0%B5%D1%82%D1%81%D1%82%D0%B2%D0%B8%D0%B5_%D0%9A%D0%B0%D1%80%D1%80%D0%B8_%E2%80%94_%D0%A5%D0%BE%D0%B2%D0%B0%D1%80%D0%B4%D0%B0

</aside>

Это то, что я хочу донести до вас. Я хочу, чтобы вы получили твердую интуицию в
том, как в действительности язык живет и дышит. Надеюсь, что позже вы прочтете и
другие, более теоретические книги, когда все концепции будут крепко держаться в 
вашем сознании, закрепленные на полученной здесь основе.

## Для чего это вообще нужно изучать?

Каждое введение в каждой подобной книге имеет такой раздел. Я не знаю, что не 
так с языками программирования, что они вызывают такие экзистенциальные 
сомнения. Не думаю, что книги по орнитологии волнуются об оправдании своего 
существования. Они полагают, что читатель любит птиц и стремится к развитию.

Но с языками программирования несколько иная ситуация. Полагаю, шансы любого из
нас на создание успешного языка программирования общего назначения невелики. 
Разработчики языков, широко применяемых во всем мире, могут вместиться в автобус
Фольксваген, даже не поднимая крышу-палатку. Если бы присоединение к этой 
элитной группе было *единственной* причиной изучать языки, это занятие 
действительно было бы сложно оправдать. К счастью, это не так.

### Маленькие языки повсюду

На каждый успешный язык общего назначения приходятся тысячи успешных нишевых
языков. Раньше мы называли их "маленькие языки", но развитие жаргона приводит
нас к названию "предметно-ориентированные языки" (domain-specific languages).
Это пиджины (промежуточные языки) специально разработанные для конкретных задач.
Например, это могут быть сценарии для приложений, движки шаблонов, форматы
разметки и конфигурационные файлы.

<span name="little"></span>
<img src="image/introduction/little-languages.png" alt="A random selection of little languages." />

<aside name="little">

Случайная выборка некоторых маленьких языков, с которыми вы можете столкнуться.

</aside>

Практически каждый крупный проект разработки ПО нуждается в нескольких из них.
Когда это возможно, лучше использовать один из существующих, чем создавать свой.
Как только вы задумываетесь о документации, отладчике, поддержке редактором,
подсветке синтаксиса и прочей атрибутике, создание всего этого самостоятельно
превращается в непростую задачу.

Но все еще остается довольно большой шанс того, что вам понадобится состряпать
парсер чего-то, в то время как под рукой не окажется библиотеки, удовлетворяющей
вашим нуждам. А если и *окажется*, вам неизбежно понадобится её отлаживать и
поддерживать, а также ковыряться в её внутренностях.

### Языки - это проекрасное упражнение

Бегуны на длинные дистанции иногда тренируются с весами, привязанными к ногам,
или на таких высотах, где воздух очень разрежён. Когда позже они освобождаются
от этого, относительная легкость в конечностях и богатый кислородом воздух
позволяют им бежать дальше и быстрее.

Разработка языка - это настоящий тест для ваших навыков программирования. Код
сложен, а производительность критична. Необходимо владеть рекурсией,
динамическими массивами, деревьями, графами и хэш-таблицами. Вы, вероятно,
будете использовать хэш-таблицы в программировании каждый день, но будете ли вы
*действительно* понимать их? Что ж, после того, как мы создадим их с нуля, я
гарантирую, что будете.

Хоть я и хочу показать, что написание языка программирования - это не так
страшно, это все еще испытание. Преодолев его вы станете более сильным
программистом, и будете умнее в том, как использовать структуры данных и
алгоритмы в своей повседневной работе.

### Еще одна причина

Последнюю причину мне тяжело признать, потому что я принимаю её близко к сердцу.
Во время обучения программированию в детстве, я чувствовал что-то волшебное в
языках. Впервые набирая свои программы на BASIC клавишу за клавишей, я не
представлял, как *сам* BASIC был устроен.

Позже, смешение трепета и страха, которое испытывали мои друзья в коллежде по
отношению к курсу компиляторов, убедило меня, что разработчики языков - это
другой вид людей. Какие-то волшебники, имеющие доступ к мистическим искусствам.

Это очаровательный <span name="image">образ</span>, но у него есть и темная
сторона. *Я* не чувствовал себя волшебником, потому думал о том, что упустил
какое-то врожденное качество, которое мешало мне присоединиться к их заговору.
Так что я был очарован языками с тех пор как набросал несколько ключевых слов в
школьной тетради, и у меня заняло десятки лет, чтобы набраться смелости и
попробовать действительно изучить их. Это "магическое" качество, чувство
особенности, отталкивало *меня*.

<aside name="image">

Его приверженцы и не планируют его развенчать. Два фундаментальных текста о
языках программирования содержат [дракона][dragon] и [волшебника][wizard] на
обложках.

[dragon]: https://en.wikipedia.org/wiki/Compilers:_Principles,_Techniques,_and_Tools
[wizard]: https://mitpress.mit.edu/sicp/full-text/book/book.html

</aside>

Когда я в конце концов начал сколачивать свои собственные маленькие
интерпретаторы, я быстро понял, что, разумеется, там вообще нет никакой магии.
Это просто код, и люди, которые пишут языки, просто люди.

*Есть* некоторые техники, с которыми вы не очень часто сталкиваетесь вне мира
написания языков, и некоторые из них довольно сложные. Но не сложнее, чем другие
препядствия, которые вы преодолеваете. Надеюсь. что эта книга позволит вам
перебороть этот страх, и, возможно, вы уйдете отсюда более храбрыми, чем были до
этого.

И, кто знает, может вы *создадите* следующий великий язык. Кто-то должен это
сделать.

## Как эта книга устроена

Эта книга разделена на три части. Сейчас вы читаете первую из них. Она 
предназначена, чтобы сориентировать тебя, научить некоторому жаргону, который
используют разработчики языков, и представить тебе Lox, язык, который мы будем
реализовывать.

Каждая из двух частей создает свой полный интерпретатор Lox. В этих частях все
главы устроены одинаково. Каждая часть берет определенную фичу языка, обучает
концептам, которые за ней лежат, и пробегает по её реализации.

Методом проб и ошибок у меня удалось разделить оба интерпретатора на куски по
главам так, чтобы каждый следующий основывался на предыдущем. Начиная с самой
первой главы, у вас будет программа, которую можно запустить и попробовать. С
каждой следующей главой она будет становиться все более полнофункциональной,
пока вы не получите в конце концов полноценный язык.

Помимо богатой и остроумной прозы, главы имеют следующие восхитительные вставки:

### Код

Мы говорим о создании интерпретаторов *своими руками*, так что в этой книге есть
настоящий код. Каждая необходимая строка обязательно будет приведена и каждая
врезка расскажет вам, куда добавить её в вашу постоянно растущую реализацию.

Многие другие книги о языках и их реализации используют такие инструменты как
[Lex][] и <span name="yacc">[Yacc][]</span>, "компиляторы компиляторов", чтобы
автоматически генерировать некоторые исходные файлы реализации из некоторого
высокоуровневого описания. Есть плюсы и минусы использования таких инструментов
и сильные аргументы -- можно сказать, религиозные убеждения -- за и против.

<aside name="yacc">

Yacc -- это инструмент, который принимает файл с грамматикой и создает исходные
файлы для компилятора, то есть что-то вроде "компилятора", дающего на выходе
компилятор. Мы будем называть это термином "компилятор компилятора".

Yacc не первый в своем роде, и именно поэтому он называется "Yacc" -- *Yet
Another* Compiler-Compiler (еще один компилятор компилятора). Более поздний
сходный инструмент [Bison][], назван от игры слов в произножении Yacc как "як".

![A yak.](image/introduction/yak.png)

[bison]: https://en.wikipedia.org/wiki/GNU_bison

Если вы находите все эти отсылки и каламбур очаровательными и забавными, вы
почувствуете себя здесь как дома. Если нет, что ж, чувство юмора у нердов
наживное.

</aside>

Мы будем воздерживаться от использования его здесь. Я стараюсь не оставить
темных углов, где могут спрятаться магия и неоднозначность, так что мы будем
писать все своими руками. Как увидите, это не так плохо, как звучит, и это
значит, что вы действительно поймете каждую строчку кода и как оба
интерпретатора работают.

[lex]: https://en.wikipedia.org/wiki/Lex_(software)
[yacc]: https://en.wikipedia.org/wiki/Yacc

Код в книге имеет требования, отличные от "реального мира", так что стиль
программирования здесь не всегда может отражать лучшее решение для написания
поддерживаемого в продакшнене программного обеспечения. Если я выгляжу
неряшливым, пропуская `private` или объявляя глобальные переменные, прошу
понять, что я так делаю, чтобы сохранять код проще в ваших глазах. Страницы
здесь не такие широкие, как в вашей IDE, и каждый символ на счету.

К тому же код содержит не так много комментариев. Это потому что каждый набор
строк окружен несколькими параграфами, которые, видит Бог, объясняют их. Когда
вы будете писать целую книгу для сопровождения вашей программы, тоже можете
опускать комментарии. В противном случае, вы должны вероятно использовать `//`
немного чаще, чем это делаю я.

Чего эта книга *не содержит*, так это механизмов компиляции и запуска кода. Я
полагаю, вы сможеет сбацать makefile или проект в вашей IDE на выбор, чтобы
запустить код.

### Врезки

Так как книга содержит буквально каждую строчку, необходимую для реализации,
все врезки предельно точны. Кроме того, так как я старался сохранять даже
наполовину написанную программу в рабочем состоянии, иногда мы добавляем
временный код, который будет заменен в последующих врезках.

Врезка со всеми примочками выгляди так:

<div class="codehilite"><pre class="insert-before"><span></span>      <span class="k">default</span><span class="o">:</span>
</pre><div class="source-file"><em>lox/Scanner.java</em><br>
in <em>scanToken</em>()<br>
replace 1 line</div>
<pre class="insert"><span></span>        <span class="k">if</span> <span class="o">(</span><span class="n">isDigit</span><span class="o">(</span><span class="n">c</span><span class="o">))</span> <span class="o">{</span>
          <span class="n">number</span><span class="o">();</span>
        <span class="o">}</span> <span class="k">else</span> <span class="o">{</span>
          <span class="n">Lox</span><span class="o">.</span><span class="na">error</span><span class="o">(</span><span class="n">line</span><span class="o">,</span> <span class="s">&quot;Unexpected character.&quot;</span><span class="o">);</span>
        <span class="o">}</span>
</pre><pre class="insert-after"><span></span>        <span class="k">break</span><span class="o">;</span>
</pre></div>

В цетре располагается новый код, добавляемый в этой врезке. Сверху и снизу могут
быть полупрозрачные строки, которые показывают, куда необходимо вставить врезку
в существующем коде. Также есть небольшая подсказка, которая подсказывает имя и
место в файле. Если она говорит "replace _ lines", значит до этого между
полупрозрачными строками был предыдущий код, который вы должны заменить на эту
врезку.

### Заметки на полях

<span name="joke">Заметки на полях</span> содержат биографические фрагменты,
историческую справку, ссылки на связанные темы и рекомендации других областей
исследования. В них нет ничего такого, что вам *нужно* знать, чтобы понимать
последующие части книги, так что можете пропускать их, если хотите. Я не буду
осуждать вас, но могу немного расстроиться.

<aside name="joke">

Что ж, по крайней мере, некоторые. Большинство из них это просто глупые шутки
и любительские рисунки.

</aside>

### Задачи

Каждая глава заканчивается несколькими упражнениями. В отличие от вопросов в
учебниках, которые рассчитаны на повтор только что прочитанного материала, они
нацелены на то, чтобы вы изучили *больше*, чем есть в главе. Они сподвигнут вас
отойти от протоптанной тропы и исследовать самим. Для их решения может быть
необходимо исследовать другие языки, понимать, как реализовать фичи языка или
любым другим образом действовать самостоятельно. 

Преодолев их вы приобретете более широкое понимание и, возможно, синяки и
царапины. Или вы можете пропустить их, если хотите оставаться в пределах
комфортного туристического автобуса. Это ваша книга. 

### Рабочие заметки

Большинство книг о языках программирования на самом деле явялются книгами о
*реализации* языков программирования. Но в них редко обсуждается, как
*спроектировать* язык, который позже будет реализован. Реализация - это
весело, потому что она <span name="benchmark">точно определена</span>. Мы,
программисты, видимо, слишком родны с вещами которые белые и черные, нули и
единицы.

<aside name="benchmark">

Я знаю много разработчиков языков, чья карьера основана на этом. Ты засовываешь
спецификацию языка под их дверь, ждешь несколько месяцев, и получаешь код с
тестами производительности на выходе.

</aside>

Лично я думаю, что мир нуждается в большом количестве реализаций <span
name="fortran">FORTRAN 77</span>. В определенный момент времени вы начинаете
разрабатывать *новый* язык. Как только вы начинаете играть в *эту* игру,
человеческие стороны уравнения становятся более значимыми. Такие вещи как
легкость изучения, баланс инновации и консерватизма, какой синтаксис более
читабелен и для кого.

<aside name="fortran">

Надеюсь, язык, в грамматике которого не будет заложена ширина перфокарты.

</aside>

Все это серьезно влияет на успех вашего нового языка. Я хочу, чтобы ваши языки
были успешными, потому некоторые главы я заканчиваю "рабочими заметками",
маленькими очерками о человеческом аспекте языков программирования. Я не эксперт
в этом -- не уверен, что кто-то дейтствительно эксперт -- так что воспринимайте
это с большой долей скепсиса. Это кроме прочего сделает их более вкусными, а это
моя основная цель. (По-английски "принимать с большой долей скепсиса" звучит так
же, как и "принимать с большой щепоткой соли" - прим. переводчика).

## The First Interpreter

We'll write our first interpreter, jlox, in Java. The focus is on *concepts*.
We'll write the simplest, cleanest code we can to correctly implement the
semantics of the language. This will get us comfortable with the basic
techniques and also hone our understanding of exactly how the language is
supposed to behave.

Java is a great language for this. It's high level enough that we don't get
overwhelmed by fiddly implementation details, but it's still pretty explicit.
Unlike scripting languages, there tends to be less magic under the hood, and
you've got static types to see what data structures you're working with.

I also chose it specifically because it is an *object-oriented* language. That
paradigm swept the programming world in the 90s and is now the dominant way of
thinking for millions of programmers. Odds are good you're already used to
organizing things into classes and methods, so we'll keep you in that comfort
zone.

While academic language folks sometimes look down on object-oriented languages,
the reality is that they are widely used even for language work. GCC and LLVM
are written in C++, as are most JavaScript virtual machines. Object-oriented
languages are ubiquitous and the tools and compilers *for* a language are often
written *in* the <span name="host">same language</span>.

<aside name="host">

A compiler reads in files in one language and translates them to files in
another language. You can implement a compiler in any language, including the
same language it compiles, a process called **"self-hosting".**

You can't compile it using itself yet, but if you have another compiler for your
language written in some other language, you use *that* one to compile your
compiler once. Now you can use the compiled version of your own compiler to
compile future versions of itself and you can discard the original one compiled
from the other compiler. This is called **"bootstrapping"** from the image of
pulling yourself up by your own bootstraps.

![Fact: This is the primary mode of transportation of the American cowboy.](image/introduction/bootstrap.png)

</aside>

And, finally, Java is hugely popular. That means there's a good chance you
already know it, so there's less for you to learn to get going in the book. If
you aren't that familiar with Java, don't freak out. I try to stick to a fairly
minimal subset of it. I use the diamond operator from Java 7 to makes things a
little more terse, but that's about it as far as "advanced" features go. If you
know another object-oriented language like C# or C++, you can probably muddle
through fine.

By the end of part II, we'll have a simple, readable implementation. What we
won't have is a *fast* one. It also leans on the Java virtual machine's runtime
facilities, but we want to learn how Java *itself* implements those things.

## The Second Interpreter

So in the next part, we'll start all over again, but this time in C. C is the
perfect language for understanding how an implementation *really* works, all the
way down to the bytes in memory and the code flowing through the CPU.

A big reason that we're using C is so I can show you things C is particularly
good at, but that *does* mean you'll need to be pretty handy with it. You don't
have to be the reincarnation of Dennis Ritchie, but you shouldn't be spooked by
pointers either.

If you aren't there yet, pick up an introductory book on C and chew through it,
then come back here when you're done. In return, you'll come away from this book
an even stronger C programmer. That's useful given how many language
implementations are written in C: Lua, CPython, and Ruby's MRI, to name a few.

In our C interpreter, <span name="clox">clox</span>, we are forced to implement
for ourselves all the things Java gave us for free. We'll write our own dynamic
array and hash table. We'll decide how objects are represented in memory, and
build a garbage collector to reclaim it.

<aside name="clox">

I pronounce the name like "sea-locks", but you can say it "clocks" or even
"clochs", where you pronounce the "x" like the Greeks do if it makes you happy.

</aside>

Our Java implementation was focused on being correct. Now that we have that
down, we'll turn to also being *fast*. Our C interpreter will contain a <span
name="compiler">compiler</span> that translates the code to an efficient
bytecode representation (don't worry, I'll get into what that means soon) which
it then executes. This is the same technique used by implementations of Lua,
Python, Ruby, PHP and many other successful languages.

<aside name="compiler">

Did you think this was just an interpreters book? It's a compiler book as well.
Two for the price of one!

</aside>

We'll even try our hand at benchmarking and optimization. By the end we'll have
a robust, accurate, fast interpreter for our language, able to keep up with
other professional caliber implementations out there. Not bad for one book and a
few thousand lines of code.

<div class="challenges">

## Challenges

1. There are least six domain-specific languages used in the [little system I
   cobbled together][repo] to write and publish this book. What are they?

1. Get a "Hello, world!" program written and running in Java. Set up whatever
   Makefiles or IDE projects you need to get it working. If you have a debugger,
   get comfortable with it and step through your program as it runs.

1. Do the same thing for C. To get some practice with pointers, define a
   [doubly-linked list][] of heap-allocated strings. Write functions to insert,
   find, and delete items from it. Test them.

[repo]: https://github.com/munificent/craftinginterpreters
[doubly-linked list]: https://en.wikipedia.org/wiki/Doubly_linked_list

</div>

<div class="design-note">

## Design Note: What's in a Name?

One of the hardest challenges in writing this book was coming up with a name for
the language it implements. I went through *pages* of candidates before I found
one that worked. As you'll discover on the first day you start building your own
language, naming is deviously hard. A good name satisfies a few criteria:

1. **It isn't in use.** You can run into all sorts of trouble, legal and social,
   if you inadvertently step on someone else's name.

2. **It's easy to pronounce.** If things go well, hordes of people will be
   saying and writing your language's name. Anything longer than a couple of
   syllables or a handful of letters will annoy them to no end.

3. **It's distinct enough to search for.** People will Google your language's
   name to find docs for it, so you want a word that's rare enough that most
   results point to your docs. Though, with the amount of AI most search engines
   are packing today, that's less of an issue. Still, you won't be doing your
   users any favors if you name your language "for".

4. **It doesn't have negative connotations across a number of cultures.** This
   is hard to guard for, but it's worth considering. The designer of Nimrod
   ended up renaming his language to "Nim" because too many people only remember
   that Bugs Bunny used "Nimrod" as an insult.

If your potential name makes it through that gauntlet, keep it. Don't get hung
up on trying to find an appellation that captures the quintessence of your
language. If the names of the world's other successful languages teach us
anything, it's that the name doesn't matter much. All you need is a reasonably
unique token.

</div>
