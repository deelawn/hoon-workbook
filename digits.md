Write a generator that can take a `@ud` and return a list of it's digits.

```
!:
:-  %say
|=  [* [n=@ud ~] ~]
:-  %noun
=|  lis=(list @ud)
|-  ^+  lis
?:  (lte n 0)
  lis
%=  $
  n    (div n 10)
  lis  (mod n 10)^lis
==
```

We're creating a `%say` generator which requires us to produce a specially formatted `cell`
```
[%say [%noun "data"]]
```

The head is the literal `%say` and the tail is a `cell` with a head indicating to the `dojo` how to print the value we are creating. In this case, we can simply say `%noun` but if we need more specific printing information we can provide that. In this example, we then provide a `tape` as the data, but this could be anything we wanted to print.

```
|=  [* [n=@ud ~] ~]
```

When running a generator, `dojo` will do type analyses to determine which arguments to give to the generator. The arguments are generally a `cell` the head of which contains a set of arguments passed to every generator. In this instance we have chosen to ignore them since they will not be needed. For more information about these argument, see the generator documentation. 

The tail of this cell is where we can describe any arguments we are expecting to be passed in when the generator is called. So we can see that we are expecting an argument `n` that is of type `@ud`.

We now have to construct the second part of the cell. Again we provide the `dojo` with the information about how to pretty print the data. Here we've selected `%noun`.

```
=|  lis=(list @ud)
```

We are going to construct a `list` of `@ud` by cutting up the `@ud` we got passed and so we add a face onto that type.

```
|-  ^+  lis
?:  (lte n 0)
  lis
%=  $
  n    (div n 10)
  lis  (mod n 10)^lis
==
```

Now we need to create the gate. We are actually able to reuse `lis` as defining the kind of thing we are going to produce. The `^+` rune is the sibling of the `^-` rune for casting. In the case of `^+` is used to cast by example and using it saves us typing out the definition of a type we already have constructed. More generally it's useful when a type is complicated.

There are two cases. If `n` is less than or equal to 0 we simply return `lis` else we need to perform some operations.

`%=` is the rune that allows us to take a wing with changes. The changes are to divide `n` by 10 to produce the next value for `n`, and to also add `(mod n 10)` onto the front of `lis`. `x^y` is equivalent to `[x y]`. `mod` takes the remainder from dividing `n` by 10. 

Doing this will essentially pull each digit out of `n` and put it into a `list`.

* TODO What if we do this with vases? Can we accept a vase, map it to whatever the correct value would be to extract each digit of the number in whatever base we are interpreting it in?