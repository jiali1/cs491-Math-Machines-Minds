{
 "cells": [
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "## LISP Interperter in Python \n",
    "\n",
    "This is a LISP interpreter written in Python 2.7. We will be using the Scheme dialect \"Lispy\" originally demonstrated by Peter Norvig in this wonderful post on the topic found here: http://norvig.com/lispy.html. \n"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "### The Structure of Scheme \n",
    "Our programs will be made up of entirely expressions, there is no statement/expression distinction in Scheme. (http://stackoverflow.com/questions/3846727/why-is-the-difference-between-an-expression-and-a-statement)  \n",
    "\n",
    "We have two atomic expressions: numbers and symbols. It is useful to note th at unlike some other languages, operators like '<' and '=' are also symbols in Scheme. \n",
    "\n",
    "Anything else will be a list expression, starting with '(', followed by zero or more expressions, and closed with a ')'. \n",
    "\n",
    "If the list begins with a keyword (eg. '(def ...)') then it is a \"special form\" (https://www.gnu.org/software/mit-scheme/documentation/mit-scheme-ref/Special-Forms.html) and the operations depend on the definition of the keyword. \n",
    "\n",
    "If the list begins with a non-keyword, then it is a function call and the operation depends on the definitions in the environment. \n",
    "\n",
    "Quoting Norvig: \"The beauty of Scheme is that the full language only needs 5 keywords and 8 syntactic forms. In comparison, Python has 33 keywords and 110 syntactic forms, and Java has 50 keywords and 133 syntactic forms. Using so many parentheses may seem unfamiliar, but it has the virtues of simplicity and consistency. (Some have joked that \"Lisp\" stands for \"Lots of Irritating Silly Parentheses\"; I think it stand for \"Lisp Is Syntactically Pure\".)\" \n",
    "\n",
    "Clarity Note: The name LISP derives from \"LISt Processor\", as stated in *The Art of Lisp Programming*\n"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "### Parsing an S-Expression \n",
    "\n",
    "The parser's main job is to ensure the syntactic correctness of the program. It first takes a expression and creates a list of tokens from that, this process is generally called *lexical analysis*. Here we pad each '(' and ')' with spaces and employ Python's split method to break apart the expression. (the split() method with no arguments creates a list of strings, splitting them original string up at every occurrence of a space)  "
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 5,
   "metadata": {
    "collapsed": True
   },
   "outputs": [],
   "source": [
    "# lexically analyzes the tokens of an S expression and returns a list of tokens\n",
    "def tokenize(expression):\n",
    "    #no need for this to be one lined, it's just really ugly otherwise\n",
    "    return expression.replace('(', ' ( ').replace(')', ' ) ').split()"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "Once the tokens are obtained, the syntactic rules of the language are used to build an abstract syntax tree (AST), which is created out of the nested structure of expressions within Scheme. This building of the AST is called *syntactic analysis*. \n",
    "\n",
    "Here we define a function create_tree() which takes the list of tokens and recursively creates a list of nested lists representing the AST based on the syntax of the expression. This process bottoms out in the calling of the atom() function, defined below, which handles the types of atomic symbols. It includes some super basic error handling dealing with syntax, but much more could be added. (see the *Features to be Added* section to see specifics) "
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 1,
   "metadata": {
    "collapsed": True
   },
   "outputs": [],
   "source": [
    "def create_tree(tokens):\n",
    "    if len(tokens) == 0:\n",
    "        print(\"Error: no tokens passed in!\")\n",
    "    token = tokens.pop(0)\n",
    "    if token == '(':\n",
    "        tree = []\n",
    "        while tokens[0] != ')':\n",
    "            tree.append(create_tree(tokens))\n",
    "        tokens.pop(0)\n",
    "        return tree\n",
    "\n",
    "    elif token == ')':\n",
    "        raise SyntaxError(\" ')' before '(' \")\n",
    "\n",
    "    else:\n",
    "        return atom(token)"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "Below we define the types we will be using and the atom() function. atom() takes in a token as an argument and returns a correctly typed atomic symbol. We first test if it is a number (int then float) and if not we return it as a symbol.  "
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 2,
   "metadata": {
    "collapsed": True
   },
   "outputs": [],
   "source": [
    "###Type Definitions###\n",
    "Symbol = str          # A Scheme Symbol is implemented as a Python str\n",
    "List   = list         # A Scheme List is implemented as a Python list\n",
    "Number = (int, float) # A Scheme Number is implemented as a Python int or float\n",
    "\n",
    "def atom(token):\n",
    "    try: return int(token)\n",
    "    except ValueError:\n",
    "        try: return float(token)\n",
    "        except ValueError:\n",
    "            return Symbol(token)"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "We now are ready to fully define the parse function, to tokenize the s-expression string into tokens and then creates the AST out of the tokens. "
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 3,
   "metadata": {
    "collapsed": True
   },
   "outputs": [],
   "source": [
    "def parse(expression):\n",
    "    tokens = tokenize(expression)\n",
    "    return create_tree(tokens)\n"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "### Enviroments\n",
    "\n",
    "Before we can define the evaluation function, we must first set up an environment by which we can define variables and functions. An environment can be throught of as a function which maps names to values. For the sake of constant retrieval time (http://www.cs.cornell.edu/courses/cs312/2008sp/lectures/lec20.html) we will use a Python dictionary to implement the environment. \n",
    "\n",
    "In order to ease the definition process, we will import both the operator library and the math library to use in the definition of the operations for our LISPY interpreter. "
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 4,
   "metadata": {
    "collapsed": True
   },
   "outputs": [],
   "source": [
    "import math\n",
    "import operator as op"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "We will see shortly that the notion of a user defined procedure and an environment are tightly coupled. As we will be allowing out interpreter to define procedures, we will need to also create a custom environment class. \n",
    "\n",
    "This class extends the dictionary object in Python and this is so we can have environments within environments. The *init* function is extended with the ability to take a list of parameter names and a list of parameter values and uses the *zip* function to create a new environment with those names mapped to their values and creates a reference to the outer environment. We also add a function *find* which looks up a value in the environment and if not found begins checking the closest outer environment for the value of the variable. "
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 5,
   "metadata": {
    "collapsed": False
   },
   "outputs": [],
   "source": [
    "class Env(dict):\n",
    "    \"An environment is a dictionary of {'var':val} pairs, with an outer Env.\"\n",
    "    def __init__(self, parms=(), args=(), outer=None):\n",
    "        self.update(zip(parms, args))\n",
    "        self.outer = outer\n",
    "    def find(self, var):\n",
    "        \"Find the innermost Env where var appears.\"\n",
    "        return self if (var in self) else self.outer.find(var)\n",
    "\n",
    "def standard_env():\n",
    "    \"An environment with some Scheme standard procedures.\"\n",
    "    env = Env()\n",
    "    env.update(vars(math)) # this inserts functions like sin, cos, sqrt, pi, ...\n",
    "    env.update({\n",
    "        '+':op.add, '-':op.sub, '*':op.mul, '/':op.div,\n",
    "        '>':op.gt, '<':op.lt, '>=':op.ge, '<=':op.le, '=':op.eq,\n",
    "        'abs':     abs,\n",
    "        'append':  op.add,\n",
    "        'apply':   apply,\n",
    "        'begin':   lambda *x: x[-1],\n",
    "        'car':     lambda x: x[0],\n",
    "        'cdr':     lambda x: x[1:],\n",
    "        'cons':    lambda x,y: [x] + y,\n",
    "        'eq?':     op.is_,\n",
    "        'equal?':  op.eq,\n",
    "        'length':  len,\n",
    "        'list':    lambda *x: list(x),\n",
    "        'list?':   lambda x: isinstance(x,list),\n",
    "        'map':     map,\n",
    "        'max':     max,\n",
    "        'min':     min,\n",
    "        'not':     op.not_,\n",
    "        'null?':   lambda x: x == [],\n",
    "        'number?': lambda x: isinstance(x, Number),\n",
    "        'procedure?': callable,\n",
    "        'round':   round,\n",
    "        'symbol?': lambda x: isinstance(x, Symbol),\n",
    "    })\n",
    "    return env\n",
    "\n",
    "global_env = standard_env()"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "Note that we do something fairly ironic in using the lambda operator to define a good deal of the operations.... \n",
    "\n",
    "For a fairly comprehensive list of operations check out: http://www.scheme.com/tspl4/control.html \n"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "### Evaluation\n",
    "\n",
    "Now all we have left to do to make a nearly complete Scheme interpreter is to evaluate the expressions passed in from the parser, using the values and operations defined in the environment.\n",
    "\n",
    "Our evaluation function will include the following special forms: \n",
    "\n",
    "| Special Form           | Syntax                       | Semantic                                                                                                                                                                  |\n",
    "|------------------------|------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------|\n",
    "| Variable Definition:   | *var*                        | Looks up the symbol as the name for variable in the environment for its definition.  ex.  *x* -> 10  (assuming x was previously defined to be 10)                         |\n",
    "| Constant Literal       | *number*                     | A number evaluates to itself.  ex. 10 -> 10 and 3.14 -> 3.14                                                                                                              |\n",
    "| Definition             | *(def var exp)*              | Modifies the environment by setting the value in the environment at the name of the variable to the value returned by the expression following it.                        |\n",
    "| Quoting                | *(quote exp)*                | Returns the expression itself, with no evaluation.                                                                                                                        |\n",
    "| Conditional            | *(if test if_true if_false)* | *if* evaluates the expression *test* and if *test* evaluates to true, then the function evaluates *if_true*. If *test* evaluates to false, then *if_false* is evaluated.  |\n",
    "| Assignment             | *(set! var exp)*             | Assignment takes a value which is **already** defined in the environment and augments its value to what *exp* evaluates to.                                               |\n",
    "| Procedure Definition   | *(lambda (var ...) exp)*     | Creates a procedure with one or more parameters *(var ...)* and  *exp* as the body of the expression.                                                                    |\n",
    "| Procedural Evaluation  | *(proc arg ...)*             | Every other type of function than those defined above is treated as a procedure. *proc* and each *arg* is evaluated and then *proc* is applied to each *arg*              |\n",
    "\n",
    "**Note**: we define a custom Procedure objects which has members params (parameters), a function body, and a custom environment. It also contains a method *call* which evaluates the body of the procedure on the environment created with the parameters, the arguments passed into the expression within a local environment *inside* the previously defined outer environment.  "
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 6,
   "metadata": {
    "collapsed": True
   },
   "outputs": [],
   "source": [
    "class Procedure(object):\n",
    "    \"A user-defined Scheme procedure.\"\n",
    "    def __init__(self, parms, body, env):\n",
    "        self.parms, self.body, self.env = parms, body, env\n",
    "    def __call__(self, *args): \n",
    "        return eval(self.body, Env(self.parms, args, self.env))\n",
    "\n",
    "\n",
    "def eval(expression, env=global_env):\n",
    "    if isinstance(expression, Symbol):\n",
    "        return env[expression] #if symbol, look it up in the eviroment\n",
    "    elif not isinstance(expression, List):\n",
    "        return expression #if not list or symbol, return the atomic quantity\n",
    "\n",
    "    elif expression[0] == 'quote':\n",
    "        (_, exp) = expression\n",
    "        return exp\n",
    "\n",
    "    elif expression[0] == 'set!':\n",
    "        (_, var, exp) = expression\n",
    "        env[var] = eval(exp, env)\n",
    "\n",
    "    elif expression[0] == 'if':\n",
    "        (_, test, conseq, alt) = expression\n",
    "        if eval(test, env):\n",
    "            return eval(conseq, env)\n",
    "        else:\n",
    "            return eval(alt, env)\n",
    "\n",
    "    elif expression[0] == 'define':\n",
    "        (_, var, exp) = expression\n",
    "        env[var] = eval(exp, env)\n",
    "\n",
    "    elif expression[0] == 'lambda':\n",
    "        (_, params, body) = expression\n",
    "        return Procedure(params, body, env)\n",
    "\n",
    "    else:\n",
    "        proc = eval(expression[0], env)\n",
    "        args = [eval(arg,env) for arg in expression[1:]]\n",
    "        return proc(*args) #* is needed for variable length argument list\n"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "### REPL \n",
    "\n",
    "One of the hallmark features of LISP is the Read, Evaluate, and Print Loop. (REPL) This creates the nice waiting and ready state that we have grown accustomed to in interpreters. Here we define the loop as well as a function *scheme_string()* which returns a syntactically correct Scheme object in the case of returning a list. \n",
    "\n",
    "We pass in a defaulted argument prompt to be printed out after evaluation at each step in the REPL function. "
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 7,
   "metadata": {
    "collapsed": True
   },
   "outputs": [],
   "source": [
    "def repl(prompt='lis.py> '):\n",
    "    while True:\n",
    "        val = eval(parse(raw_input(prompt)))\n",
    "        if val != None:\n",
    "            print(scheme_string(val))\n",
    "\n",
    "def scheme_string(expression):\n",
    "    if isinstance(expression, List):\n",
    "        return '(' + ' '.join(map(scheme_string, expression)) + ')'\n",
    "    else:\n",
    "        return str(expression)"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "### Run the thing! \n",
    "\n",
    "We now have a fairly fully functioning Scheme interpreter! Just call *repl()* to test it out :-)  "
   ]
  },
  {
   "cell_type": "code",
   "execution_count": None,
   "metadata": {
    "collapsed": False
   },
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "lis.py> (define x (quote(11 12)))\n",
      "lis.py> (cons (quote (1)) x)\n",
      "((1) 11 12)\n",
      "lis.py> (cons (quote 1) x)\n",
      "(1 11 12)\n"
     ]
    }
   ],
   "source": [
    "repl()"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "### Features to be Added: \n",
    "* More Types: strings, characters, booleans, ports, vectors, exact/inexact numbers, and more!!! \n",
    "\n",
    "* **Many** more procedures. There are over 100 scheme procedures which are not defined here (https://www.cs.indiana.edu/scheme-repository/R4RS/r4rs_8.html). Some of these are due to constraints with Python (eg. *set-cdr!*) , but most can (and should!) be implemented.     \n",
    "\n",
    "* modified by Jia Li, originally implemented in Peter Norwig's lisp interpretator 2. \n",
    "def callcc(proc):\n",
    "Call proc with current continuation; escape only\n",
    "b = RuntimeWarning(Sorry, this continuation is no longer valid.)\n",
    "def throw(retval): b.retval = retval; raise b\n",
    "try:\n",
    "   return proc(throw)\n",
    "except RuntimeWarning as w:\n",
    "   if w is b: return b.retval\n"
    "   else: raise w\n"
    "\n",
    "def let(*args):\n",
    "args = list(args)\n",
    "x = cons(_let, args)\n",
    "require(x, len(args)>1)\n",
    "bindings, body = args[0], args[1:]\n",
    "require(x, all(isa(b, list) and len(b)==2 and isa(b[0], Symbol)\n",
    "              for b in bindings), illegal binding list)\n",
    "vars, vals = zip(*bindings)\n",
    "return [[_lambda, list(vars)]+map(expand, body)] + map(expand, vals)\n",
    "\n",
    "* **Any** amount of error handling. You just gotta be perfect, thats all! \n",
    "    * error handling while parsing \n",
    "    * error handling while evaluating \n",
    "\n",
    "* Memory of past expressions entered."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": None,
   "metadata": {
    "collapsed": True
   },
   "outputs": [],
   "source": []
  }
 ],
 "metadata": {
  "kernelspec": {
   "display_name": "Python 2",
   "language": "python",
   "name": "python2"
  },
  "language_info": {
   "codemirror_mode": {
    "name": "ipython",
    "version": 2
   },
   "file_extension": ".py",
   "mimetype": "text/x-python",
   "name": "python",
   "nbconvert_exporter": "python",
   "pygments_lexer": "ipython2",
   "version": "2.7.12"
  }
 },
 "nbformat": 4,
 "nbformat_minor": 0
}

