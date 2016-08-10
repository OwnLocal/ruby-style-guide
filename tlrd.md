# Style Guide TL;DR v0.1

This is a summary of the most important bits of the full [OwnLocal ruby style guide](README.md).

The TL;DR of the TL;DR is: write consistent, easily readable, understandable, pretty, maintainable code.

See also: [Ruby on Rails 3 Style Guide](https://github.com/bbatsov/rails-style-guide).


## Table of Contents

* [Source Code Layout](#source-code-layout)
* [Syntax](#syntax)
* [Naming](#naming)
* [Comments](#comments)
    * [Comment Annotations](#comment-annotations)
* [Classes](#classes--modules)
* [Exceptions](#exceptions)
* [Collections](#collections)
* [Strings](#strings)
* [Regular Expressions](#regular-expressions)
* [Percent Literals](#percent-literals)
* [Metaprogramming](#metaprogramming)
* [Misc](#misc)

## Source Code Layout

* Use two **spaces** per indentation level. No hard tabs.
* Limit lines to 100 characters.
* Use Unix-style line endings (I'm talking to you, Windows users).
* Use one expression per line - don't use `;` to separate statements and expressions. 
  ... EXCEPT, prefer a single-line format for class definitions with no body:

    ```Ruby
    class FooError < StandardError; end
    ```

* Use spaces around operators, after commas, colons and semicolons, around `{`
  and before `}` (except when defining a hash or interpolated string).  Do not
  use spaces inside `()` or `[]`.  Also no space after `!`

    ```Ruby
    sum = 1 + 2
    a, b = 1, 2
    1 > 2 ? true : false; puts 'Hi'
    [1, 2, 3].each { |e| puts e }
    some(arg).other if !something
    puts "string#{expr}"
    def some_method(arg1 = :default, arg2 = nil, arg3 = [])
      # ...
    end
    ```

* Indent `when` at the same level as `case`, and always preserve indentation of conditionals, array elements, parameters, etc.

    ```Ruby
    kind = case year
           when 1850..1889 then 'Blues'
           when 1890..1909 then 'Ragtime'
           else 'Jazz'
           end
           
    def send_mail(source)
      Mailer.deliver(to: 'bob@example.com',
                     subject: 'Important message',
                     body: source.text)
    end

    # or, if lines are too long...
    kind =
      case year
      when 1850..1889
        'Blues'
      when 1890..1909
        'Ragtime'
      else
        'Jazz'
      end
      
    def send_mail(source)
      Mailer.deliver(
        to: 'bob@example.com',
        subject: 'Important message',
        body: source.text
      )
    end
    ```

* Use one empty line between method definitions and also to break up a method into logical
  paragraphs internally.
* Avoid using `\` line continuations for anything but string concatenation.
* Avoid trailing whitespace.
* Don't use block comments.  And remove all commented out code.

## Syntax

* Use `::` only to reference constants and constructors (like `Array()` or `Nokogiri::HTML()`).
  Never use `::` for regular method invocation.
* Never use `for`.
* Never use `then` for multi-line `if/unless`.
* Always put the condition on the same line as the `if`/`unless`.
* Ternary operators should only be used for very simple, readable expressions.
* Avoid the use of `!!`.
* The `and` and `or` keywords are acceptable for flow control.
  Always use `&&` and `||` in boolean expressions.

    ```Ruby
    # boolean expression
    if some_condition && some_other_condition
      #control flow
      document.save or raise DocumentSavingError, "The document didn't save! (lolwut)"
    end

    # control flow
    render :error and return
    ```

* Favor `unless` over `if !` and `until` over `while !`
* Never use `unless` with `else`. Rewrite these with the positive case first.
* Never use parentheses around a condition when assigning a variable inside the conditional expression:

    ```Ruby
    #bad
    if (b = Business.find(id))
      # b...
    end

    #good
    b = Business.find(id)
    if b
      # b...
    end
    ```

* Omit parentheses for method calls with no arguments.
* Prefer `{...}` for single-line blocks, and `do...end` for multi-line
  blocks. And *never* chain multi-line blocks.
* Avoid `self` where not required. (It is only required when calling a self write accessor.)
* Avoid shadowing method names with local variable names (unless they are equivalent data).
* Prefer `proc.call()` over `proc[]` or `proc.()` for both lambdas and procs.
* Use `_` for unused block parameters, like this: `result = hash.map { |_, v| v + 1 }`
* Use `$stdout/$stderr/$stdin` instead of `STDOUT/STDERR/STDIN`.
* Favor english method names (`Array#join`, `format`) over obscure operator methods (`Array#*`, `String#%`)
* Use ranges or `.between?` instead of complex comparison logic when possible.

    ```Ruby
    do_something if (1000..2000).include?(x)
    do_something if x.between?(1000, 2000)
    ```

* Avoid the use of the [flip-flop operator](http://nithinbekal.com/posts/ruby-flip-flop/).

* Use guard clauses when possible to avoid nested conditional flow control:

    ```Ruby
    # good
      def compute(thing)
        return unless thing[:foo] # guard clause
        update_with_bar(thing[:foo])
        return re_compute(thing) unless thing[:foo][:bar] # guard clause
        partial_compute(thing)
      end
    ```

## Naming

* Name things in English.
* Use `snake_case` for symbols, methods and variables.
* Use `CamelCase` for classes and modules.  (Keep acronyms like HTTP,
  RFC, XML uppercase.)
* Use `SCREAMING_SNAKE_CASE` for other constants.
* The names of predicate methods (methods that return a boolean value)
  should end in a question mark.
  (i.e. `Array#empty?`). Methods that don't return a boolean, shouldn't
  end in a question mark.
* Prefer `map` over `collect`, `find` over `detect`, `select` over
  `find_all`, `reduce` over `inject` and `size` over `length`. If the use
  of the alias enhances readability, it's ok to use it.

## Comments

* Write self-documenting code and ignore the rest of this section. Seriously!
* Write comments in English.
* Use one space between the leading `#` character of the comment and the text
  of the comment.
* Comments longer than a word are capitalized and use punctuation. Use [one
  space](http://en.wikipedia.org/wiki/Sentence_spacing) after periods.
* Avoid superfluous comments such as: `counter += 1 # Increments counter by one.`
* Instead of writing comments to explain unreadable code, refactor the code to
  make it self-explanatory. (Do or do not - there is no try)

## Classes & Modules

* Use a consistent structure in your class definitions.

    ```Ruby
    class Person
      # extend and include go first
      extend SomeModule
      include AnotherModule

      # constants are next
      SOME_CONSTANT = 20

      # then attribute macros
      attr_reader :name

      # followed by other macros (if any)
      validates :name

      # public class methods are next
      def self.some_method
      end

      # followed by public instance methods
      def some_method
      end

      # protected then private methods are grouped near the end
      private

      def some_private_method
      end
    end
    ```

* Make your classes as
  [SOLID](http://en.wikipedia.org/wiki/SOLID_(object-oriented_design\))
  as possible.

* Use the `attr_reader` and `attr_accessor` to define trivial accessors and mutators.
* Consider using `Struct.new`, which defines the trivial accessors,
constructor and comparison operators for you.
* Avoid the usage of class (`@@`) variables due to their "nasty" behavior
in inheritance.
* Assign proper visibility levels to methods (`private`, `protected`)
in accordance with their intended usage. Don't go off leaving
everything `public` (which is the default).
* Indent the `public`, `protected`, and `private` methods as much the
  method definitions they apply to, surrounded by blank lines.
* Use `def self.method` to define class methods rather than repeating the class name.

## Exceptions

* Supply an exception class and a message as two separate
  arguments to `fail/raise` (instead of providing an exception instance).

* Never return from an `ensure` block.
* Use *implicit begin blocks* where possible (don't add a suerfluous begin block around an entire method).
* Don't suppress exceptions.
* Avoid using `rescue` in its modifier form.
* Don't use exceptions for flow control.
* Don't rescue from `Exception`.
* Release external resources obtained by your program in an ensure block (i.e. close opened files)
* Favor the use of standard library exceptions over introducing new exception classes.

## Collections

* Avoid Array.new and Hash.new when possible (use `[]` and `{}`)
* Use `.first` or `.last` over `[0]` or `[-1]`
* Avoid mutable objects as hash keys, and prefer symbols instead of strings.
* Use the shortened syntax when your hash keys are symbols: `{one: 1, two: 2}`
* Use `Hash#key?` instead of `Hash#has_key?` and `Hash#value?` instead of `Hash#has_value?`.
* Never modify a collection while traversing it.

## Strings

* Prefer string interpolation instead of string concatenation: `email_with_name = "#{ user.name } <#{ user.email }>"`
* Don't use the character literal syntax `?x`.  Just use `'x'`
* Don't leave out `{}` around interpolated variables, even when valid.
* Use `<<` instead of `+` when you need to construct large data strings.

## Regular Expressions

* Don't use regular expressions if you just need plain text search in string:
  `string['text']`
* For simple constructions you can use regexp directly through string index.

    ```Ruby
    match = string[/regexp/]             # get content of matched regexp
    first_group = string[/text(grp)/, 1] # get content of captured group
    string[/text (grp)/, 1] = 'replace'  # string => 'text replace'
    ```

* For complex replacements use `sub`/`gsub` with a block or hash.

## Percent Literals

* Mostly just don't use them unless it's necessary -
  except for `%w` and `%i` for arrays of strings and symbols, respectively.

## Metaprogramming

* Avoid it.
  - Seriously
* Avoid monkey-patching core classes - especially in libraries.
* Avoid using `method_missing` for metaprogramming. Consider using delegation, proxy, or `define_method` instead.

## Misc

* Avoid hashes as optional parameters. Does the method do too much? (Object initializers are exceptions for this rule).
* Avoid methods longer than 10 LOC (lines of code). Ideally, most methods will be <= 5 LOC.
* Avoid parameter lists longer than three or four parameters.
* If you really need "global" methods, add them to Kernel
  and make them private (but most likely, you don't).
* Use module instance variables instead of global variables.

    ```Ruby
    # bad
    $foo_bar = 1

    #good
    module Foo
      class << self
        attr_accessor :bar
      end
    end

    Foo.bar = 1
    ```

* Do not mutate arguments unless that is the purpose of the method.
* Avoid more than three levels of block nesting.
* Be consistent. In an ideal world, be consistent with these guidelines.
