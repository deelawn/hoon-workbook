Write a generator that emulates a Magic 8 Ball.

```
!:
:-  %say
|=  [[* eny=@uv *] *]
:-  %noun
^-  tape
=/  answers=(list tape)
  :~  "It is certain."
      "It is decidedly so."
      "Without a doubt."
      "Yes - definitely."
      "You may rely on it."
      "As I see it, yes."
      "Most likely."
      "Outlook good."
      "Yes."
      "Signs point to yes."
      "Reply hazy, try again"
      "Ask again later."
      "Better not tell you now."
      "Cannot predict now."
      "Concentrate and ask again."
      "Don't count on it."
      "My reply is no."
      "My sources say no."
      "Outlook not so good."
      "Very doubtful."
  ==
=/  rng  ~(. og eny)
=/  val  (rad:rng (lent answers))
(snag val answers)
```

As always when developing something, we start with the `!:` rune to enable a full stack trace for any errors.

Because we are writing a generator, the output has to be in a specific format. In this case we are writing a `%say` generator and so what `dojo` is looking for is a `cell` where the first element is the type of generator and the second is dependent on that type. In the case of a `%say` generator, we need to have our second argument also be a `cell`, the first element of which is a literal for the type we are going to be outputting and the second is the actual output. Specifying the type, lets `dojo` understand how we want our value to be pretty printed.

```
|=  [[* eny=@uv *] *]
```

We now use `|=` to form a gate with a sample. The sample here is going to be arguments that every generator gets passed. The format we are expecting is a `cell`, the first element of which is itself a `cell` which has three elements which are

1.  *
2.  eny=@uv
3.  * 

The second element of the outer cell is also `*` but what does this actually mean? Here `*` respresents any `noun`. Essentially we are saying these are arguments that will get passed that we don't care what they are. In the case of this generator all we are intersted in is the second argument of the inner `cell` which is `eny=@uv` so it's the only one we put a face on.

What is this second argument? The second argument passed to all generators is a random value used to provide some entropy. We will shortly use this to seed a random number generator.

```
:-  %noun
```

Recall that the output a `%say` generator must be in a particular format. Having formed the head of the response, `%say` we must now construct tha latter half which is itself a `cell` with a head of `%noun`. Here we use the generic type `%noun` to tell `dojo` how to print our value. If we had a need to do so we could specify a type with better formating information for the pretty printer, but it's not neccessary here.

```
^-  tape
```

The second half of our `cell` we decide must be a `tape` or it should not compile properly.

```
=/  answers=(list tape)
  :~  "It is certain."
      "It is decidedly so."
      "Without a doubt."
      "Yes - definitely."
      "You may rely on it."
      "As I see it, yes."
      "Most likely."
      "Outlook good."
      "Yes."
      "Signs point to yes."
      "Reply hazy, try again"
      "Ask again later."
      "Better not tell you now."
      "Cannot predict now."
      "Concentrate and ask again."
      "Don't count on it."
      "My reply is no."
      "My sources say no."
      "Outlook not so good."
      "Very doubtful."
  ==
```

This section in particular should be very easy for you to read. We are going to put a value onto the subject with the face `answers` and it will be a `list` of `tapes`. The values are the 20 answers possible from a Magic 8 Ball.

The `:~` is a very useful rune that constructs a null-terminated list. Here we have used the tall form for reability but you could use the wide form `~[1 2 3]` where appropriate. Remember that if your code will be easier to read by using the tall form of a rune, you should use the tall form. One of the major benefits of coding this way is the code will flow down the screen instead of across it, making it much easier to follow.

```
=/  rng  ~(. og eny)
=/  val  (rad:rng (lent answers))
```

We now use `eny` to seed our random number generator. `og` is the core which contains a number of rng arms that are available for our use. What we are doing is equivalent to creating a random number generator object with a particular seed. We are then able give it a face and afterwards pull arms out of it, in this case `rad` and put a face on that value also. `rad` is the arm in `og` that will produce a random number from 0 to one less than the upper bound we give it, in this case, the length of `answers`.

```
(snag val answers)
```

`snag` lets us index into a `list` and get the value at that location. Here we use the value generated by our random number generator to fetch that element from the `answers` list. that gets added as the second half of our cell we began constructing above and that entire data structure gets passed on to the `dojo` for handling. The `dojo` will then print out whatever value we have given it.