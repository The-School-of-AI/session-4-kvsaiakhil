# Session 4 Assignment of EPAi3.0

## Numeric Types - I

| [Repo Contents](https://github.com/The-School-of-AI/session-3-kvsaiakhil/) | Description          |
| ------------------------------------------------------------ | -------------------- |
| [README.md](https://github.com/The-School-of-AI/session-4-kvsaiakhil/blob/main/README.md) | readme file          |
| [session4.py](https://github.com/The-School-of-AI/session-4-kvsaiakhil/blob/master/session4.py) | assignment code file |
| [test_session4.py](https://github.com/The-School-of-AI/session-4-kvsaiakhil/blob/master/test_session4.py) | assignment test file |

### session4.py

#### `import` statements

Module `random` to generate pseudo random numbers with a lot more functionality. 

[Documentation](https://docs.python.org/3/library/random.html)

```python
import random
```

Module `decimal` provides exact decimal representation internally (the human form :stuck_out_tongue_winking_eye: ) to a set precision unlike float

[Documentation](https://docs.python.org/3/library/decimal.html)

```python
from decimal import *
```



#### Function: manual_round()

Gave a shot at manually implementing in-built `round()` function:   

##### Function implementation

```python
def manual_round(number, ndigits=None):
   
    if not (type(number) is int or type(number) is float):
        raise TypeError(f"must be real number, not {type(number).__name__}")

    if ndigits is None:
        ndigits = 0

    if type(ndigits) is not int:
        raise TypeError(f"must be int, not {type(number).__name__}")

    if number == 0.0:
        return number

    sign  = (-1, 1)[number>0]
    number = abs(number)

    if ndigits <= 0:

        pow_10_multiple = 10**(-ndigits)
        int_quotient = number//pow_10_multiple
        floorn = pow_10_multiple*int_quotient
        ceiln = pow_10_multiple*(int_quotient+1)

        if (number - floorn) == (ceiln - number):
            rval = sign*(floorn if floorn % (2*pow_10_multiple) == 0 else ceiln)
        elif (number - floorn) > (ceiln - number):
            rval = sign*ceiln
        else: 
            rval = sign*floorn

        if ndigits == 0:
            return int(rval)

        return rval

    if ndigits > 0:
        f_str = "{:.100f}".format(number)
        intp, decp = f_str.split(".")

        if len(decp) <= ndigits:
            return sign*float(f_str)

        p5 = float("0." + decp[ndigits:])

        round_str = decp[:ndigits]

        if p5 == 0.5:
            if int(decp[ndigits-1])%2 == 0:
                rstr = intp + "." + round_str
            else:
                rstr = intp + "." + str(int(round_str)+1).zfill(len(round_str))
        elif p5 < 0.5:
            rstr = intp + "." + round_str
        elif p5 > 0.5:
            rstr = intp + "." + str(int(round_str)+1).zfill(len(round_str))
        return sign*float(rstr)
```

This function emulates python's inbuilt `round(number[,ndigits])` function. Values are rounded to the closest multiple of *10 to the power minus* `ndigits`.

It implements Banker's rounding algorithm:

* If ndigits is omitted or is None, it returns the nearest integer to its input.
* Values are rounded to the closest multiple of -> 10 to the power negative ndigits
* If two multiples are equally close, rounding is done toward the even choice

Any integer value is valid for `ndigits` (positive, zero, or negative)  

##### Algorithm

Checking if `number` is a real value & `ndigits` is a integer.

```python
if not (type(number) is int or type(number) is float):
	raise TypeError(f"must be real number, not {type(number).__name__}")

if type(ndigits) is not int:
	raise TypeError(f"must be int, not {type(number).__name__}")
```

If `ndigits` is not provided, by default rounding happens to nearest integer -> 10<sup>0</sup> multiple

```python
if ndigits is None:
    ndigits = 0
```

Checking if the `number` is 0.0 and returning it 

```python
 if number == 0.0:
 	return number
```

Extracting the sign of the `number`

```python
sign  = (-1, 1)[number>0]
```

Using only the magnitude of `number` as rounding is symmetrical over left and right of the real number line.

```python
number = abs(number)
```

As `ndigits` can take values -> negative, zero and positive, the logic is divided into two parts for:

1.  `ndigits` <= 0 &
2. `ndigits` > 0

###### Case: `ndigits <=0`

Rounding to the nearest multiple of **positive power of 10** -> 10<sup>-ndigits</sup>

```python
if ndigits <= 0:
 
    pow_10_multiple = 10**(-ndigits)
    int_quotient = number//pow_10_multiple
    floorn = pow_10_multiple*int_quotient
    ceiln = pow_10_multiple*(int_quotient+1)

    if (number - floorn) == (ceiln - number):
        rval = sign*(floorn if floorn % (2*pow_10_multiple) == 0 else ceiln)
    elif (number - floorn) > (ceiln - number):
        rval = sign*ceiln
    else: 
        rval = sign*floorn

    if ndigits == 0:
        return int(rval)

    return rval
```

###### Case: `ndigits > 0`

Rounding to the nearest multiple of **negative power of 10** -> 10<sup>-ndigits</sup>

```python
if ndigits > 0:
    f_str = "{:.100f}".format(number)
    intp, decp = f_str.split(".")

    if len(decp) <= ndigits:
        return sign*float(f_str)

    p5 = float("0." + decp[ndigits:])

    round_str = decp[:ndigits]

    if p5 == 0.5:
        if int(decp[ndigits-1])%2 == 0:
            rstr = intp + "." + round_str
        else:
            rstr = intp + "." + str(int(round_str)+1).zfill(len(round_str))
    elif p5 < 0.5:
        rstr = intp + "." + round_str
    elif p5 > 0.5:
        rstr = intp + "." + str(int(round_str)+1).zfill(len(round_str))
    
    return sign*float(rstr)
```



#### Class: Qualean

**A class similar to Boolean.** - But it has a *quantum nature* added to it, thereby its name **"Qualean"**.

It takes in one of the three values `[-1, 0, 1]` -> Maybe, False and True respectively.

Instead of storing them as they are:

* it internally picks a pseudo random between `[-1, 1)`

* multiplies it with the input value and stores the result

##### Usage

```python
#### VALID INPUTS ####

q = Qualean()

q1 = Qualean(-1)
q2 = Qualean(0)
q3 = Qualean(1)

q4 = Qualean(True) # as True == 1
q5 = Qualean(False) # as False == 0

#### INVALID INPUTS ####

q_err1 = Qualean(100) # Output : Invalid input: input value must be one of [-1, 0, 1]
q_err2 = Qualean('simply') # Output : Invalid input: input value must be one of [-1, 0, 1]

```

##### Implementing dunder functions 

###### \_\_init__

Initializes the Qualean object. It takes one argument `number` .

Default value of `number` is 1. 

Class `Qualean` has two data members:

* `number` -> public -> user input -> directly accessible using member access operator `q.number`
* `__qualean` -> private -> computed value -> not directly accessible, use `return_qualean()` to get the value



`number` is the user input and can be one of [-1, 0, 1].

`__qualean` is generated by:

1. first choosing a random between [-1,1) using `random.uniform(-1,1)`
2. rounding the above `value` to 10 decimal places: `manual_round(value,10)`
3. then storing the result to `__qualean`

```python
def __init__(self, number: int = 1):
        if number not in [-1, 0, 1]:
            raise ValueError(f"Invalid input: input value must be one of [-1, 0, 1]")
        self.number =  number
        self.__qualeen = manual_round(self.number*random.uniform(-1,1), 10)
```



###### return_qualean()

Returns qualean value.

```python
def return_qualean(self):
	return self.__qualeen
```



###### \_\_bool__ 

Returns boolean of qualean value.

```python
def __bool__(self):
	return bool(self.__qualeen)
```



###### \_\_str__

String representation of Qualean object.

```python
def __str__(self):
	return f'Qualean String for number: ' + str(self.number)
```



###### \_\_repr__

String describing the Qualean object.

```python
def __repr__(self):
	return f'Qualean Class Instance'
```



###### \_\_float__

Returns float of qualean value.

```python
def __float__(self):
	return self.__qualeen
```



###### \_\_invertsign__

Inverts the sign of qualean value and returns the result 

```python
def __invertsign__(self):
	return -self.__qualeen
```



###### Logical Operators

###### \_\_and__

Function implements Logical **"and"** operator for Qualean objects.

Returns `False` if the second operand is `NoneType`.

Returns `True` only if both the operands are `True`

```python
def __and__(self, q2=None):
    if q2 is None:
        return False
    if not isinstance(q2, Qualean):
        return bool(self.__qualeen and q2)
    return bool(self.__qualeen and q2.return_qualean())
```



###### \_\_or__

Function implements Logical **"or"** operator for Qualean objects

Returns boolean of first operand if the second operand is NoneType.

Returns `True` if any of the operands is `True`

```python
def __or__(self, q2=None):
    if q2 is None:
        return bool(self.__qualeen)
    if not isinstance(q2, Qualean):
        return bool(self.__qualeen or q2)
    return bool(self.__qualeen or q2.return_qualean())
```



###### Arithmetic Operators

###### \_\_add__

Function implements Arithmetic **"addition"** operator for Qualean objects.

The second operand can either be a Qualean object or a real number.

Returns the sum of qualean values of the operands (of type Qualean)

(OR)

If the second operand is real number, returns the sum of qualean value of the first operand and the real value

```python
def __add__(self, q2):
    if q2 is None or not (isinstance(q2, int) or isinstance(q2, float) or isinstance(q2, Qualean)):
        raise TypeError(f"unsupported operand type(s) for __add__: 'Qualean' and '{type(q2).__name__}'")
    if not isinstance(q2, Qualean):
        return self.__qualeen + q2
    return self.__qualeen + q2.return_qualean()
```



###### \_\_mul__

Function implements Arithmetic **"multiplication"** operator for Qualean objects.

The second operand can either be a Qualean object or a real number.

Returns the product of qualean values of the operands (of type Qualean)

(OR)

If the second operand is real number, returns the sum of qualean value of the first operand and the real value

```python
def __mul__(self, q2):
    if q2 is None or not (isinstance(q2, int) or isinstance(q2, float) or isinstance(q2, Qualean)):
        raise TypeError(f"unsupported operand type(s) for __mul__: 'Qualean' and '{type(q2).__name__}'")
    if not isinstance(q2, Qualean):
        return self.__qualeen * q2
    return self.__qualeen * q2.return_qualean()
```



###### \_\_sqrt__

Finds the **square root** of qualean value for both positive and negative values. 

```python
def __sqrt__(self):
    if self.__qualeen>=0:
        return abs(Decimal(self.__qualeen)).sqrt()
    else:
        return str(manual_round(float(Decimal(self.__invertsign__()).sqrt()), 10)) + 'i'

    # to return the value in float
    # return manual_round(Decimal(self.__invertsign__()).sqrt(), 10)*1j
```



###### Comparison Operators

###### \_\_eq__

Function implements comparison **"is equal to"** operator on Qualean objects.

The second operand can either be a Qualean object or a real number.

Returns True if both qualean values of the operands (of type Qualean) are equal

(OR)

If the second operand is real number, returns True if the qualean value of the first operand (of type Qualean) and the real value are equal

```python
 def __eq__(self, q2):
 	if q2 is None or not (isinstance(q2, int) or isinstance(q2, float) or isinstance(q2, Qualean)):
        raise TypeError(f"unsupported operand type(s) for __eq__: 'Qualean' and '{type(q2).__name__}'")
    if not isinstance(q2, Qualean):
        return self.__qualeen == q2
    return self.__qualeen == q2.return_qualean()
```



###### \_\_ge__

Function implements comparison **"is greater than or equal to"** operator on Qualean objects.

The second operand can either be a Qualean object or a real number.

Returns True if the first operand is greater than or equal to the second operand (both of type Qualean).

(OR)

If the second operand is real number, returns True if the qualean value of the first operand (of type Qualean) is greater than or equal to the real value.

```python
def __ge__(self, q2):
    if q2 is None or not (isinstance(q2, int) or isinstance(q2, float) or isinstance(q2, Qualean)):
        raise TypeError(f"unsupported operand type(s) for __ge__: 'Qualean' and '{type(q2).__name__}'")
    if not isinstance(q2, Qualean):
        return self.__qualeen >= q2
    return self.__qualeen >= q2.return_qualean()
```



###### \_\_gt__

Function implements comparison **"is greater than"** operator on Qualean objects..

The second operand can either be a Qualean object or a real number.

Returns True if the first operand is greater than the second operand (both of type Qualean).

(OR)

If the second operand is real number, returns True if the qualean value of the first operand (of type Qualean) is greater than the real value

```python
def __gt__(self, q2):

    if q2 is None or not (isinstance(q2, int) or isinstance(q2, float) or isinstance(q2, Qualean)):
        raise TypeError(f"unsupported operand type(s) for __gt__: 'Qualean' and '{type(q2).__name__}'")
    if not isinstance(q2, Qualean):
        return self.__qualeen > q2
    return self.__qualeen > q2.return_qualean()
```



###### \_\_le__

Function implements comparison **"is less than or equal to"** operator on Qualean objects.

The second operand can either be a Qualean object or a real number.

Returns True if the first operand is less than or equal to the second operand (both of type Qualean).

(OR)

If the second operand is real number, returns True if the qualean value of the first operand (of type Qualean) is less than or equal to the real value.

```python
def __le__(self, q2):
    if q2 is None or not (isinstance(q2, int) or isinstance(q2, float) or isinstance(q2, Qualean)):
        raise TypeError(f"unsupported operand type(s) for __le__: 'Qualean' and '{type(q2).__name__}'")
    if not isinstance(q2, Qualean):
        return self.__qualeen <= q2
    return self.__qualeen <= q2.return_qualean()
```



###### \_\_lt__

Function implements comparison **"is less than or equal to"** operator on Qualean objects.

The second operand can either be a Qualean object or a real number.

Returns True if the first operand is less than or equal to the second operand (both of type Qualean).

(OR)

If the second operand is real number, returns True if the qualean value of the first operand (of type Qualean) is less than or equal to the real value.

```python
def __lt__(self, q2):
    if q2 is None or not (isinstance(q2, int) or isinstance(q2, float) or isinstance(q2, Qualean)):
        raise TypeError(f"unsupported operand type(s) for __lt__: 'Qualean' and '{type(q2).__name__}'")
    if not isinstance(q2, Qualean):
        return self.__qualeen < q2
    return self.__qualeen < q2.return_qualean()
```



